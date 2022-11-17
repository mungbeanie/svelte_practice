<script lang="ts">
    import { Undo2, Redo2, Sun, Moon } from "lucide-svelte";

    type TCircle = {
        x: number;
        y: number;
        hex: string;
    };

    let circles: Array<TCircle> = [];
    let poppedCircles: Array<TCircle> = [];
    let darkMode = false;

    const handleClickCircle = (event: { clientX: number; clientY: number }) => {
        const { clientX, clientY } = event;
        const new_circle = {
            x: clientX,
            y: clientY,
            hex: generateRandomColor(),
        };
        circles = [...circles, new_circle];
        console.log(`Added ${JSON.stringify(new_circle)}`);
    };

    const handleUndo = () => {
        if (circles.length == 0) return;
        const circles_array = circles;
        const popped = circles_array.pop();
        console.log(`Undid ${JSON.stringify(popped)}`);
        circles = circles_array;
        poppedCircles = [...poppedCircles, popped];
    };

    const handleRedo = () => {
        if (poppedCircles.length == 0) return;
        const popped_circles_array = poppedCircles;
        const popped = popped_circles_array.pop();
        console.log(`Redid ${JSON.stringify(popped)}`);
        poppedCircles = popped_circles_array;
        circles = [...circles, popped];
    };

    const handleRemoveCircle = (circle_to_remove: TCircle) => {
        console.log(`Removed ${JSON.stringify(circle_to_remove)}`);
        circles = circles.filter(
            (circle) => circle.hex !== circle_to_remove.hex
        );
        let new_popped_circles = poppedCircles;
        new_popped_circles.push(circle_to_remove);
        poppedCircles = new_popped_circles;
    };

    const generateRandomColor = () =>
        "#" + ((Math.random() * 0xffffff) << 0).toString(16).padStart(6, "0");
</script>

<main
    class="main"
    style:background-color={darkMode ? "black" : "white"}
    on:click={handleClickCircle}
    on:keydown={null}
>
    {#each circles as circle, index}
        <button
            class="circle"
            style:left="calc({circle.x}px - calc(var(--circle-size) / 2))"
            style:top="calc({circle.y}px - calc(var(--circle-size) / 2)"
            style:background-color={circle.hex}
            style:border="2px solid {darkMode ? "white" : "black"}"
            title="{index.toString()} #{circle.hex}"
            on:click|stopPropagation={() => handleRemoveCircle(circle)}
        />
    {/each}
</main>
<footer class="footer">
    <button
        class="footer-button"
        on:click={() => {
            darkMode = !darkMode;
        }}
    >
        {#if darkMode}
            <Sun />
        {:else}
            <Moon />
        {/if}
    </button>
    <button
        class="footer-button"
        on:click={handleUndo}
        disabled={circles.length == 0}><Undo2 /></button
    >
    <button
        class="footer-button"
        on:click={handleRedo}
        disabled={poppedCircles.length == 0}><Redo2 /></button
    >
</footer>

<style>
    .main {
        height: calc(100% - var(--footer-height));
    }

    .circle {
        position: absolute;
        border-radius: 100%;
        height: var(--circle-size);
        width: var(--circle-size);
    }
    .circle:hover {
        cursor: pointer;
    }

    .footer-button {
        border-radius: 100%;
        border: none;
        cursor: pointer;
    }
    .footer-button:disabled {
        cursor: default;
    }

    .footer {
        height: var(--footer-height);
        width: 100%;
        background-color: purple;
        display: flex;
        justify-content: flex-end;
        align-items: center;
        position: fixed;
        z-index: 999;
    }
    .footer > * {
        margin: 4px;
    }
</style>
