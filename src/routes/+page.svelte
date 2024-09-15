<script>
    import Highlight from "@highlight-ai/app-runtime";

    let recording = false;
    let recordingInterval;

    async function store100Integers() {
        for (let i = 0; i < 100; i++) {
            Highlight.appStorage.set(`${i}`, i);
        }
    }

    async function toggleRecording() {
        if (!recording) {
            try {
                const screenshotPermissions = await Highlight.permissions.requestScreenshotPermission();
                const backgroundPermissions = await Highlight.permissions.requestBackgroundPermission();
                if (screenshotPermissions && backgroundPermissions) {
                    recordingInterval = setInterval(async () => {
                        const windows = await Highlight.user.getWindows();
                        if (windows[0]) {
                            const focusedWindowTitle = windows[0].windowTitle;
                            const focusedWindowScreenshot = await Highlight.user.getWindowScreenshot(focusedWindowTitle);
                            Highlight.appStorage.set(`screenshots/${Date.now()}`, focusedWindowScreenshot);
                            console.log(`snapshot: ${focusedWindowTitle}`);
                        }
                    }, 1000);
                }
            } catch (error) {
                console.log(error.message)
            }
        } else {
            clearInterval(recordingInterval);
        }
        recording = !recording;
    }

    async function clearStorage() {
        Highlight.appStorage.clear();
    }
</script>

<div class="flex flex-col justify-center items-center gap-10 w-screen h-screen bg-black">
    <button class="w-[400px] text-white text-lg p-2 bg-indigo-500 bg-opacity-80 hover:bg-opacity-50 rounded-full"
    on:click={store100Integers}>
        store 100 integers
    </button>
    <button class="w-[400px] text-white text-lg p-2 bg-opacity-80 hover:bg-opacity-50 rounded-full"
    class:bg-green-500={!recording}
    class:bg-orange-500={recording}
    on:click={toggleRecording}>
        {#if !recording}
            start recording
        {:else}
            stop recording
        {/if}
    </button>
    <button class="w-[400px] text-white text-lg p-2 bg-red-500 bg-opacity-80 hover:bg-opacity-50 rounded-full"
    on:click={clearStorage}>
        clear storage
    </button>
</div>