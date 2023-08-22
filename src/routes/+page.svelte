<script lang="ts">
    import { onMount } from "svelte";

    let red: number = 255;
    let green: number = 255;
    let blue: number = 255;

    let target_red: number = 0;
    let target_green: number = 0;
    let target_blue: number = 0;

    let current: HTMLCanvasElement;
    let target: HTMLCanvasElement;

    let guessInfo: string = "";

    function toHex(r: number, g: number, b: number): string {
        return `#${r.toString(16).padStart(2, "0")}${g
            .toString(16)
            .padStart(2, "0")}${b.toString(16).padStart(2, "0")}`;
    }

    $: hex_string = toHex(red, green, blue);
    $: drawCurrentColour(red, green, blue);

    function drawCurrentColour(red: number, green: number, blue: number) {
        if (current == undefined) {
            return;
        }
        let ctx = current.getContext("2d")!;

        ctx.fillStyle = hex_string;
        ctx.fillRect(0, 0, current.width, current.height);
    }

    onMount(() => {
        reset();
    });

    function reset() {
        target_red = Math.floor(Math.random() * 256);
        target_green = Math.floor(Math.random() * 256);
        target_blue = Math.floor(Math.random() * 256);

        let ctx = target.getContext("2d")!;

        ctx.fillStyle = toHex(target_red, target_green, target_blue);
        ctx.fillRect(0, 0, target.width, target.height);

        guessInfo = "";
    }

    function guess() {
        let score = Math.max(
            0,
            100 -
                Math.abs(target_red - red) -
                Math.abs(target_green - green) -
                Math.abs(target_blue - blue)
        );
        guessInfo = `you guessed ${toHex(
            red,
            green,
            blue
        )}, while the actual colour was ${toHex(
            target_red,
            target_green,
            target_blue
        )}. this earns you a score of <b>${score} points</b>.`;
    }
</script>

<div class="bg-rose-100 p-32 w-screen h-screen absolute left-0 top-0">
    <h1 class="text-4xl font-bold mb-4">chroma</h1>
    <h2 class="text-xl mb-12">
        a simple colour guessing game by <a
            class="text-blue-500 underline hover:cursor-pointer"
            href="https://www.twitter.com/simeondermaats">@simeondermaats</a
        >
    </h2>

    <div class="flex flex-row w-full gap-12">
        <div class="p-4 rounded-sm shadow-md bg-white">
            <canvas
                id="target"
                class="h-32 border-black border mb-6"
                bind:this={target}
            />
            <p class="mb-4">
                this is the <b>target colour</b>. your goal is to create this
                colour using the sliders on your right.
            </p>
            <button
                class="px-4 py-2 bg-rose-500 hover:bg-rose-800 transition-all text-white"
                on:click={guessInfo == "" ? guess : reset}
            >
                {guessInfo == "" ? "attempt guess" : "try again"}
            </button>
            <p>
                {@html guessInfo}
            </p>
        </div>
        <div class="p-4 rounded-sm shadow-md bg-white">
            <canvas
                id="current"
                class="h-32 border-black border mb-6"
                bind:this={current}
            />
            <div class="flex flex-col gap-4">
                <div>
                    <p>R</p>
                    <input type="range" max="255" bind:value={red} />
                    <p>{red}</p>
                </div>
                <div>
                    <p>G</p>
                    <input type="range" max="255" bind:value={green} />
                    <p>{green}</p>
                </div>
                <div>
                    <p>B</p>
                    <input type="range" max="255" bind:value={blue} />
                    <p>{blue}</p>
                </div>
            </div>
            <p>
                {hex_string}
            </p>
        </div>
    </div>
</div>
