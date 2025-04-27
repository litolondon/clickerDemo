<script>
    import { onMount } from "svelte";

    const defaultState = {
        totalRot: 0,
        rotCoins: 0,
        perSec: 5000000,
        screens: 1,
        screenPoor: false,
        x10Poor: false,
        autoTechUnlocked: false,
        screenCost: 50,
        x10Cost: 1000,
        autoTechCost: 2000,
        followerCost: 1500
    };

    function loadState() {
        if (typeof window === "undefined") return defaultState;
        const savedState = localStorage.getItem('gameState');
        return savedState ? JSON.parse(savedState) : defaultState;
    }

    let user = loadState();
    let videoButtonClicked = false; // <-- for jiggling the button

    $: {
        if (typeof window !== "undefined") {
            localStorage.setItem('gameState', JSON.stringify(user));
        }
    }

    onMount(() => {
        alert("Brain Rot Clicker\n\nBuy more screens, watch more videos, expand your knowledge of brainrot.");
    });

    function formatNumber(num) {
        return num.toLocaleString();
    }

    function increment() {
        user.rotCoins += (1 * user.screens);
        user.totalRot += (1 * user.screens);
        
        // Trigger jiggle animation
        videoButtonClicked = true;
        setTimeout(() => {
            videoButtonClicked = false;
        }, 100); // animation duration
    }

    function timer() {
        user.rotCoins += user.perSec;
        user.totalRot += user.perSec;
    }
    setInterval(timer, 1000);

    function buyScreen() {
        if (user.rotCoins >= user.screenCost) {
            user.rotCoins -= user.screenCost;
            user.screens += 1;
            user.screenCost = Math.floor(user.screenCost * 1.15);
            user.screenPoor = false;
        } else {
            user.screenPoor = true;
            alert(`You must have at least $${formatNumber(user.screenCost)} for a new screen!`);
        }
    }

    function buyX10() {
        if (user.rotCoins >= user.x10Cost) {
            user.rotCoins -= user.x10Cost;
            user.screens += 10;
            user.x10Cost = Math.floor(user.x10Cost * 1.15);
            user.x10Poor = false;
        } else {
            user.x10Poor = true;
            alert(`You must have at least $${formatNumber(user.x10Cost)} for 10 new screens!`);
        }
    }

    function buyAutoTech() {
        if (user.rotCoins >= user.autoTechCost) {
            user.rotCoins -= user.autoTechCost;
            user.autoTechUnlocked = true;
        } else {
            alert(`You must have at least $${formatNumber(user.autoTechCost)} to unlock Auto-Rot Technologies!`);
        }
    }

    function buyFollower() {
        if (user.rotCoins >= user.followerCost) {
            user.perSec += 25;
            user.rotCoins -= user.followerCost;
            user.followerCost = Math.floor(user.followerCost * 1.25);
        } else {
            alert(`You must have at least $${formatNumber(user.followerCost)} for a Rot Cult Follower!`);
        }
    }

    function resetGame() {
        if (typeof window !== "undefined") {
            localStorage.removeItem('gameState');
        }
        user = { ...defaultState };
    }
</script>

<div class="game-grid">
    <!-- Screen Emoji Section (Left Margin) -->
    <div class="screens-section">
        {#each Array(user.screens) as _}
            <span class="screen-emoji">📺</span>
        {/each}
    </div>

    <!-- Game Content Section -->
    <div class="game-container">
        <!-- Game Stats Section -->
        <div class="stats">
            <h1>Brain Rot Clicker</h1>
            <p>Perceived Rot: {formatNumber(user.totalRot)}</p>
            <p>Screens Owned: {user.screens}</p>
            <p>Rotcoins: ${formatNumber(user.rotCoins)}</p>
            <p>Rot per Second: {formatNumber(user.perSec)}</p>
        </div>

        <!-- Clicker Shop (First Shop) -->
        <div class="shop">
            <h2>Clicker Shop</h2>
            <div class="shop-item">
                <p class="seek-rot-title">Seek Rot:</p>
                <button 
                    class={videoButtonClicked ? "video-jiggle" : ""}
                    onclick={increment}>
                    Next Video
                </button>
            </div>
            <div class="shop-item">
                <p>Extra Screen: </p>
                <button 
                    class={user.rotCoins >= user.screenCost ? "can-afford" : "cant-afford"}
                    onclick={buyScreen}>
                    ${formatNumber(user.screenCost)} Rotcoins
                </button>
            </div>
            <div class="shop-item">
                <p>10 Extra Monitors: </p>
                <button 
                    class={user.rotCoins >= user.x10Cost ? "can-afford" : "cant-afford"}
                    onclick={buyX10}>
                    ${formatNumber(user.x10Cost)} Rotcoins
                </button>
            </div>

            <!-- Unlock Auto-Rot Technologies -->
            {#if !user.autoTechUnlocked}
                <div class="shop-item">
                    <p>Unlock Auto-Rot Technologies:</p>
                    <button 
                        class={user.rotCoins >= user.autoTechCost ? "can-afford" : "cant-afford"}
                        onclick={buyAutoTech}>
                        ${formatNumber(user.autoTechCost)} Rotcoins
                    </button>
                </div>
            {/if}
        </div>

        <!-- Idle Tech Shop (Only Available After Unlocking Auto-Rot Technologies) -->
        {#if user.autoTechUnlocked}
            <div class="tech-upgrades">
                <h2>Auto-Rot Technologies</h2>
                <div class="shop-item">
                    <p>Rot Cult Follower:</p>
                    <button 
                        class={user.rotCoins >= user.followerCost ? "can-afford" : "cant-afford"}
                        onclick={buyFollower}>
                        ${formatNumber(user.followerCost)} Rotcoins
                    </button>
                </div>
            </div>
        {/if}

        <!-- Reset Button -->
        <div class="reset">
            <button onclick={resetGame}>Reset Game</button>
        </div>
    </div>
</div>

<style>
    .game-grid {
        display: grid;
        grid-template-columns: 1fr 3fr; /* One column for the emoji section, three for the game content */
        gap: 20px;
        max-width: 1200px;
        margin: 0 auto;
        padding: 20px;
        align-items: start;
    }

    .screens-section {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(40px, 1fr)); /* Auto-fill with wrapping */
        grid-gap: 10px;
        padding: 10px;
    }

    .screen-emoji {
        font-size: 24px;
        text-align: center;
    }

    .game-container {
        font-family: 'Arial', sans-serif;
        padding: 20px;
        background-color: #f4f4f4;
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    }

    h1, h2 {
        color: #4caf50;
        font-size: 24px;
    }

    .stats, .shop, .tech-upgrades, .reset {
        margin-bottom: 20px;
    }

    .shop-item {
        margin: 10px 0;
    }

    button {
        padding: 10px 20px;
        font-size: 16px;
        cursor: pointer;
        transition: transform 0.2s ease, background-color 0.2s ease;
        border-radius: 8px;
        margin: 5px 0;
    }

    .seek-rot-title {
        color: #4caf50;
    }

    .can-afford {
        background-color: #4caf50; /* Green */
        color: white;
    }

    .cant-afford {
        background-color: #f44336; /* Red */
        color: white;
    }

    .can-afford:hover,
    .cant-afford:hover {
        animation: pulse 1s infinite;
    }

    @keyframes pulse {
        0% { transform: scale(1); }
        50% { transform: scale(1.05); }
        100% { transform: scale(1); }
    }

    .video-jiggle {
        animation: jiggle 0.1s;
    }

    @keyframes jiggle {
        0% { transform: translateX(0); }
        25% { transform: translateX(-5px); }
        50% { transform: translateX(5px); }
        75% { transform: translateX(-5px); }
        100% { transform: translateX(0); }
    }

    .reset {
        margin-top: 20px;
        text-align: center;
    }

    .reset button {
        background-color: #f44336; /* Red */
        color: white;
        font-size: 18px;
    }
</style>
