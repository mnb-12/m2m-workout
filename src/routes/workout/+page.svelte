<script>
    const images = import.meta.glob("$lib/variations/*.webp", { eager: true });

    let currentCircuit = $state(0);
    let currentExercise = $state(0);
    let variationHistory = $state([
        "1a",
        "2a",
        "3a",
        "4a",
        "5a",
        "6a",
        "7a",
        "8a",
        "9a",
        "10a",
    ]);
    const exerciseInfo = [
        {
            name: "HS Pushup Progressions (6-12 reps)",
            variations: ["1a", "1b", "1c", "1d"],
        },
        {
            name: "Pistol Squat Progressions (6-12 reps/side)",
            variations: ["2a", "2b", "2c", "2d"],
        },
        {
            name: "Pushup Progressions (6-12 reps/side)",
            variations: ["3a", "3b", "3c", "3d", "3e"],
        },
        {
            name: "Chin-up Progressions (6-12 reps/side)",
            variations: ["4a", "4b", "4c", "4d"],
        },
        {
            name: "Bodyweight Hip Thrust Progressions (6-12 reps/side)",
            variations: ["5a", "5b", "5c"],
        },
        {
            name: "Plank / Awkward Plank (30s or 15s/side)",
            variations: ["6a", "6b", "6c"],
        },
        {
            name: "Russian Twists (12 reps/side)",
            variations: ["7a"],
        },
        {
            name: "Bicycle Crunches (20 reps/side)",
            variations: ["8a"],
        },
        {
            name: "Reverse Plank (30s)",
            variations: ["9a"],
        },
        {
            name: "Rest, Bridge or Full Wheel (15-20s)",
            variations: ["10a", "10b", "10c"],
        },
    ];

    // prevent screen lock and load variation data from localStorage
    $effect(async () => {
        await navigator.wakeLock.request("screen");

        document.addEventListener("visibilitychange", async () => {
            if (document.visibilityState === "visible") {
                await navigator.wakeLock.request("screen");
            }
        });

        if (localStorage.getItem("selectedVariations")) {
            variationHistory = JSON.parse(
                localStorage.getItem("selectedVariations"),
            );
        }
    });

    function nextCircuit() {
        currentCircuit++;
        currentExercise = 0;
    }

    function nextExercise() {
        currentExercise++;
    }
</script>

{#if currentCircuit > 3}
    <header>You may now close this page. <a href="/">Return </a> soon.</header>
    <h1>Workout Complete!</h1>
{:else}
    <header>
        Circuit {currentCircuit + 1}/4 • Set {currentExercise + 1}/10
    </header>

    <h1>{exerciseInfo[currentExercise].name}</h1>

    <img
        src={images[
            `/src/lib/variations/${variationHistory[currentExercise]}.webp`
        ]["default"]}
        alt="Exercise {variationHistory[currentExercise]}"
        fetchpriority="high"
    />

    <div class="controls">
        <select
            aria-label="variation"
            bind:value={variationHistory[currentExercise]}
            onchange={() => {
                localStorage.setItem(
                    "selectedVariations",
                    JSON.stringify(variationHistory),
                );
            }}
        >
            {#each exerciseInfo[currentExercise].variations as variation}
                <option value={variation}> {variation}</option>
            {/each}
        </select>

        <button
            onclick={() =>
                currentExercise === exerciseInfo.length - 1
                    ? nextCircuit()
                    : nextExercise()}
        >
            Next
        </button>
    </div>
{/if}

<style>
    .controls {
        display: flex;
        justify-content: space-between;
    }

    img {
        width: 100%;
    }

    button,
    select {
        appearance: none;
        font-weight: 700;
        width: 12rem;
        text-align: center;
        margin: 0;
        padding: 0.5em 1.5em;
        border: 2px solid #030303;
        border-radius: 99rem;
        color: white;
        background-color: #030303;
    }

    button:hover,
    select:hover {
        cursor: pointer;
    }

    button:focus,
    select:focus {
        color: #030303;
        background-color: white;
    }
</style>
