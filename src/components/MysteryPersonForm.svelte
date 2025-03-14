<script lang="ts">

    import MysteryPerson from './MysteryPerson.svelte';

    // these values are reactive to user input
    let selectedCat = $state('');
    let selectedGender = $state('');

    const cats = ["Lion", "Cheetah", "Panther"];

    const genders = ["A", "B", "C"];

</script>

<div class="hero-container">
<h1>Choose your own adventure</h1>
<div class="form">
    <MysteryPerson gender={selectedGender} cat={selectedCat}/>

    <div class="field">
        <label>Gender:</label>
        <div class="button-group">
            {#each genders as gender}
                <button
                    type="button"
                    class:selected={selectedGender === gender}
                    onclick={() => selectedGender = gender}
                >
                    {gender}
                </button>
            {/each}
        </div>
    </div>

    <div class="field">
        <label>Cat:</label>
        <div class="button-group">
            {#each cats as cat}
            <!-- We force user to select gender first -->
            <button
                type="button"
                    disabled={!selectedGender} 
                    class:selected={selectedCat === cat}
                    onclick={() => selectedCat = cat}
                >
                    {cat}
                </button>
            {/each}
        </div>
    </div>
</div>
</div>

<style>
    button:disabled, 
    button.disabled {
        opacity: 0.5;
        cursor: not-allowed;
        background-color: #ccc; /* Greyed out */
        color: #666;
        border-color: #999;
    }

    h1 {
                font-size: 2.5rem;
                font-weight: bold;
                text-align: center;
                color: #333;
                text-transform: uppercase;
                letter-spacing: 2px;
                padding: 10px 20px;
                background: black;
                -webkit-background-clip: text;
                -webkit-text-fill-color: transparent;
                border-bottom: 3px solid #ff7e5f;
                display: inline-block;
                margin: 20px auto;
            }


    .hero-container {
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: white;
            text-transform: uppercase;
            gap: 24px;
            margin-top: 5px;
    }

    .form {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 12px;
        margin-top: 20px;
    }

    /* Each row */
    .field {
        display: flex;
        align-items: center;
        gap: 8px;
    }

    label {
        font-weight: bold;
        white-space: nowrap;
        color: black;
    }

    /* Button group */
    .button-group {
        display: flex;
        gap: 8px;
    }

    button {
        padding: 6px 12px;
        color: black;
        border: 1px solid #ccc;
        background-color: b;
        cursor: pointer;
        transition: background-color 0.2s;
    }

    button.selected {
        background-color: #4f46e5;
        color: white;
        font-weight: bold;
    }

</style>
