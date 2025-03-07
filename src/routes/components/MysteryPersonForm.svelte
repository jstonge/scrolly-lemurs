<script lang="ts">
    import { createEventDispatcher, onMount } from 'svelte';

    const dispatch = createEventDispatcher();

    let selectedCat = "";
    let selectedGender = "";

    const genders = ["A", "B", "C"];
    const cats = ["Lion", "Tiger", "Cheetah", "Leopard", "Panther"];

    let canvas: HTMLCanvasElement;

    const genderedColors = {
        A: 'pink',
        B: 'blue',
        C: 'gold'
    };

    function selectGender(gender: string) {
        selectedGender = gender;
        redraw();
        notifyChange();
    }

    function notifyChange() {
        dispatch('change', { cat: selectedCat, gender: selectedGender });
    }

    function redraw() {
        if (canvas) {
            const ctx = canvas.getContext('2d');
            if (ctx) drawMysteryPerson(ctx);
        }
    }

    function drawMysteryPerson(ctx: CanvasRenderingContext2D) {
        // Clear canvas
        ctx.clearRect(0, 0, 150, 150);

        // Background
        ctx.fillStyle = '#f9f9f9';
        ctx.fillRect(0, 0, 150, 150);

        // Hat color (based on gender)
        const genderedColor = genderedColors[selectedGender] || '#111';

        // Body
        ctx.fillStyle = genderedColor;
        ctx.fillRect(60, 80, 30, 50);

        // Head
        ctx.fillStyle = genderedColor;
        ctx.beginPath();
        ctx.arc(75, 60, 18, 0, Math.PI * 2);
        ctx.fill();

        // Arms and legs
        ctx.strokeStyle = '#444';
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(60, 90);
        ctx.lineTo(45, 110);
        ctx.moveTo(90, 90);
        ctx.lineTo(105, 110);
        ctx.moveTo(65, 130);
        ctx.lineTo(65, 145);
        ctx.moveTo(85, 130);
        ctx.lineTo(85, 145);
        ctx.stroke();

        // Add cat-specific flair (fun part!)
        if (selectedCat === "Lion") {
            ctx.fillStyle = 'gold';
            ctx.beginPath();
            ctx.arc(75, 60, 24, 0, Math.PI * 2);
            ctx.fill(); // Mane
        } else if (selectedCat === "Cheetah") {
            ctx.fillStyle = 'orange';
            ctx.beginPath();
            ctx.arc(75, 60, 18, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = 'black';
            for (let i = 0; i < 5; i++) {
                ctx.beginPath();
                ctx.arc(70 + i * 4, 55 + (i % 2) * 4, 2, 0, Math.PI * 2);
                ctx.fill();
            }
        } else if (selectedCat === "Panther") {
            ctx.fillStyle = 'black';
            ctx.fillRect(0, 0, 150, 150);
        }

        // Floating question mark (only if no cat selected)
        if (!selectedCat) {
            ctx.font = 'bold 24px Arial';
            ctx.fillStyle = '#d00';
            ctx.fillText('?', 70, 24);
        }
    }

    onMount(redraw);
</script>

<div class="hero-container">
    <h1>Choose your own adventure!</h1>

    <div class="canvas-container">
        <canvas bind:this={canvas} width="150" height="150"></canvas>
    </div>

    <div class="widget-container">
        <div class="field">
            <label for="cat-label" class="field-label">Major Cat</label>
            <select bind:value={selectedCat} class="field-input" on:change={() => { notifyChange(); redraw(); }}>
                <option value="" disabled>Select a cat...</option>
                {#each cats as cat}
                    <option value={cat}>{cat}</option>
                {/each}
            </select>
        </div>

        <div class="field">
            <label for="gender-label" class="field-label">Gender</label>
            <div class="gender-buttons">
                {#each genders as gender}
                    <button
                        type="button"
                        class:selected={selectedGender === gender}
                        on:click={() => selectGender(gender)}
                    >
                        {gender}
                    </button>
                {/each}
            </div>
        </div>

        <p class="summary">
            Selected Cat: {selectedCat || "None"}, Gender: {selectedGender || "None"}
        </p>
    </div>
</div>

<style>
    h1 {
        font-size: 2.5rem;
        font-weight: bold;
        text-align: center;
        color: whitesmoke;
        text-transform: uppercase;
        letter-spacing: 2px;
        padding: 10px 20px;
        background: whitesmoke;
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        border-bottom: 3px solid #b46b6b;
        display: inline-block;
        margin: 20px auto;
    }

    .hero-container {
        height: 100vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        background: linear-gradient(135deg, #1a1a2e, #16213e);
        color: white;
        text-transform: uppercase;
        font-family: 'Press Start 2P', sans-serif; /* Optional retro game font */
        position: relative;
        overflow: hidden;
        gap: 24px;
        margin-top: 5px;
}

.canvas-container {
    width: 150px;
    height: 150px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #f0f0f0;
    border: 1px solid #ccc;
    border-radius: 8px;
}

.widget-container {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
}

.field {
    display: flex;
    align-items: center;
    gap: 8px;
}

.field-label {
    font-weight: bold;
    white-space: nowrap;
}

.field-input {
    padding: 6px 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

.gender-buttons {
    display: flex;
    gap: 6px;
}

.gender-buttons button {
    padding: 6px 12px;
    border: 1px solid #ccc;
    border-radius: 4px;
    cursor: pointer;
    background-color: #f9f9f9;
    color: black;
    transition: background-color 0.2s;
}

.gender-buttons button.selected {
    background-color: #4f46e5;
    color: white;
}

.summary {
    font-size: 12px;
    color: whitesmoke;
    margin-top: 4px;
}
</style>
