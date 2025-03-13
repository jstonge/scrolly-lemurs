<script lang="ts">
    export let gender: string = "";  // "A", "B", "C"
    export let cat: string = "";      // "Lion", "Cheetah", "Panther", etc.

    const catColors = {
        "Lion": "pink",
        "Cheetah": "orange",
        "Panther": "black"
    };

    $: bodyColor = catColors[cat] || "#444";  // Default gray if no cat selected
    $: isDancing = gender && cat;  // Only dance if both are selected
</script>

<div class="person-wrapper">
    {#if !gender && !cat}
        <div class="big-question">?</div>
    {:else}
    <div class="person {isDancing ? 'dancing' : ''}" style="--body-color: {bodyColor}">
            <div class="head {gender ? `head-${gender}` : ''}">
                <div class="face">
                    <div class="eyes">
                        <div class="eye"></div>
                        <div class="eye"></div>
                    </div>
                    <div class="mouth"></div>
                </div>
            </div>
            <div class="body"></div>
            <div class="legs">
                <div class="leg"></div>
                <div class="leg"></div>
            </div>
        </div>
    {/if}
</div>

<style>
.person-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 200px;
    width: 200px;
    position: relative;
    background-color: #f9f9f9;
    border: 2px solid #4e4848;
    border-radius: 8px;
    border-style: dashed;
    column-rule-style: dashed
}

/* Big question mark for initial state */
.big-question {
    font-size: 64px;
    font-weight: bold;
    color: black;
    animation: pulse 1.5s infinite;
}

@keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.1); }
    100% { transform: scale(1); }
}

/* The whole person */
.person {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0;
    animation: bobbing 1.5s infinite ease-in-out;
}

/* The big dance! */
.person.dancing {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0;
    animation: wiggle 0.5s infinite ease-in-out;
}

/* Wiggle keyframes - full-body dance */
@keyframes wiggle {
    0% { transform: rotate(0deg); }
    25% { transform: rotate(-5deg) translateX(-2px); }
    50% { transform: rotate(0deg); }
    75% { transform: rotate(5deg) translateX(2px); }
    100% { transform: rotate(0deg); }
}

@keyframes bobbing {
    0%, 100% {
        transform: translateY(0);
    }
    50% {
        transform: translateY(-5px);
    }
}

.head, .body {
    width: 40px;
    background-color: var(--body-color, #444);
}


/* Head shapes */
.head {
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    background-color: var(--body-color, #444);
}

/* Square for A */
.head-A {
    width: 50px;
    height: 50px;
}

/* Triangle for B */
.head-B {
    width: 50px;
    height: 50px;
    background-color: var(--body-color, #444);
    clip-path: polygon(50% 0%, 100% 100%, 0% 100%);
    display: flex;
    align-items: center;
    justify-content: center;
}


/* Hexagon for C */
.head-C {
    width: 50px;
    height: 70px;
    background-color: var(--body-color, #444);
    /* clip-path: polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%); */
    border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
}

/* Face (shifted lower inside the head) */
.face {
    position: absolute;
    top: 60%;  /* <--- Lowered the face */
    left: 50%;
    transform: translate(-50%, -50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
}

.eyes {
    display: flex;
    justify-content: space-evenly;
    width: 60%;
}

.eye {
    width: 3px;
    height: 5px;
    background-color: white;
    border-radius: 50%;
}

.mouth {
    width: 12px;
    height: 2px;
    background-color: white;
    margin-top: 4px;
}

/* Legs */
.legs {
    display: flex;
    justify-content: space-between;
    width: 20px;
}

.leg {
    width: 5px;
    height: 30px;
    background-color: var(--body-color, #444);
}
</style>
