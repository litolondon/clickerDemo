<script>
    import { onMount } from "svelte";
    
    // Define default values for the game state
    const defaultState = {
        totalRot: 0,
        rotCoins: 0,
        perSec: 0,
        multi: 1,
        x2: false,
        x10: false,
        autoTechUnlocked: false
    };

    // Load state from localStorage or use defaults (only on client)
    function loadState() {
        if (typeof window === "undefined") {
            return defaultState; // Prevents accessing localStorage during SSR
        }

        const savedState = localStorage.getItem('gameState');
        return savedState ? JSON.parse(savedState) : defaultState;
    }

    // Store the game state in a reactive object
    let state = loadState();

    // Watch for changes and save to localStorage (only on client)
    $: {
        if (typeof window !== "undefined") {
            localStorage.setItem('gameState', JSON.stringify(state));
        }
    }

    // Load initial message when mounted
    onMount(function welcome() {
        alert("Brain Rot Clicker\n\nBuy more screens, watch more videos, expand your knowledge of brainrot.");
    });

    // Functions for the game logic
    function increment() {
        state.rotCoins += (1 * state.multi);
        state.totalRot += (1 * state.multi);
    }

    function timer() {
        state.rotCoins += state.perSec;
        state.totalRot += state.perSec;
    }
    setInterval(timer, 1000);

    function buyField() {
        if (state.rotCoins >= 50) {
            state.rotCoins -= 50;
            state.multi += 1;
            state.x2 = false;
        } else {
            state.x2 = true;
        }

        if (state.x2) {
            alert("You must have at least $50 for a new screen!");
        }
    }

    function buyX10() {
        if (state.rotCoins >= 1000) {
            state.rotCoins -= 1000;
            state.multi += 10;
            state.x10 = false;
        } else {
            state.x10 = true;
        }

        if (state.x10) {
            alert("You must have at least $1000 for 10 new screens!");
        }
    }

    function buyAutoTech() {
        if (state.rotCoins >= 15000) {
            state.rotCoins -= 15000;
            state.autoTechUnlocked = true;
        }

        if (!state.autoTechUnlocked && state.rotCoins < 15000) {
            alert("You must have at least $15000 to unlock Auto-Rot Technologies!");
        }
    }

    function buyFollower() {
        if (state.rotCoins >= 15000) {
            state.perSec += 25;
            state.rotCoins -= 15000;
        }
    }

    function resetGame() {
        if (typeof window !== "undefined") {
            localStorage.removeItem('gameState'); // Clear localStorage
        }
        state = { ...defaultState }; // Reset state to default values
    }
</script>

<h1>Brain Rot Clicker</h1>
<div>
    <p>Percieved Rot: {state.totalRot}</p>
    <p>screens owned: {state.multi}</p> 
    <p>Rotcoins: ${state.rotCoins}</p>
    <p>Rot per second: {state.perSec}</p>
</div>

<div>
    <p style="display: inline; color:red">Seek Rot:</p>
    <button onclick={increment}>
        Next Video
    </button>
</div>

<div>
    <p style="display: inline;">Extra Screen: </p>
    <button onclick={buyField}>
        $50 Rotcoins
    </button>
</div>

<div>
    <p style="display: inline;">10 Extra Monitors: </p>
    <button onclick={buyX10}>
        $1000 Rotcoins
    </button>
</div>

<div>
    <div>
        <p style="display: inline;">Unlock Auto-Rot Technologies: </p>
        {#if !state.autoTechUnlocked}
        <button onclick={buyAutoTech}>
            $15000 Rotcoins
        </button>
        {:else}
        <p style="display: inline;">Unlocked</p>
        {/if}
    </div>
    {#if state.autoTechUnlocked}
    <div>
        <div>
            <p>Auto-Rot Technologies</p>
        </div>
        <div>
            <p style="display: inline;">Rot Cult Follower: </p>
            <button onclick={buyFollower}>
                $15000 Rotcoins
            </button>
        </div>
    </div>
    {/if}
</div>

<div>
    <button onclick={resetGame}>Reset Game</button>
</div>