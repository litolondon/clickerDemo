<script>
    import { onMount } from "svelte";
    import Tooltip from "./Tooltip.svelte";

    const defaultState = {
        totalRot: 1000000,
        rotCoins: 1000000,
        perSec: 0,
        screens: 1,
        followers: 0, // Add followers to the state
        screenPoor: false,
        x10Poor: false,
        autoTechUnlocked: false,
        screenCost: 50,
        x10Cost: 1000,
        autoTechCost: 2000,
        followerCost: 1500,
        legionCost: 50000
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
            user.followers += 1;
            user.perSec += 25; // Each follower adds 25 rot per second
            user.rotCoins -= user.followerCost;
            user.followerCost = Math.floor(user.followerCost * 1.25);
        } else {
            alert(`You must have at least $${formatNumber(user.followerCost)} for a Rot Cult Follower!`);
        }
    }

    function buyLegion() {
        if (user.rotCoins >= user.legionCost) {
            user.followers += 10;
            user.perSec += 250; // Each follower adds 25 rot per second
            user.rotCoins -= user.legionCost;
            user.legionCost = Math.floor(user.legionCost * 1.25);
        } else {
            alert(`You must have at least $${formatNumber(user.legionCostCost)} for a Rot Cult Legion!`);
        }
    }

    function resetGame() {
        if (window.confirm("Are you sure you want to reset?\nTHIS IS NOT A PRESTIGE!!!")) {
            localStorage.removeItem('gameState');
            user = { ...defaultState };
            alert("Brain Rot Clicker\n\nBuy more screens, watch more videos, expand your knowledge of brainrot.");
        } else {
            alert("RESET ABORTED!!!\nBack to rotting...");
        }
    }

    // Helper to create the emoji array with a max of 100 screens
    $: screenEmojis = {
        emojis: Array.from({ length: Math.min(user.screens, 100) }, (_, index) => {
            return {
                id: index,
                backgroundColor: getColorForCount(user.screens, index)
            };
        })
    };

    // Helper to create the emoji array for followers
    $: followerEmojis = {
        emojis: Array.from({ length: Math.min(user.screens, 100) }, (_, index) => {
            return {
                id: index,
                backgroundColor: getColorForCount(user.followers, index)
            };
        })
    };

    // Function to get the background color based on the count
    function getColorForCount(count, index) {
        if (count <= 100) {
            return "transparent";
        } else if (count <= 200) {
            return index < (count - 100) ? "yellow" : "transparent";
        } else if (count <= 300) {
            return index < (count - 200) ? "lightgreen" : "yellow";
        } else if (count <= 400) {
            return index < (count - 300) ? "#71BE25" : "lightgreen";
        } else if (count <= 500) {
            return index < (count - 400) ? "#22390b" : "#71BE25";
        } else if (count <= 600) {
            return index < (count - 500) ? "blue" : "#22390b";
        } else if (count <= 700) {
            return index < (count - 600) ? "darkblue" : "blue";
        } else if (count <= 800) {
            return index < (count - 700) ? "#9c66d2" : "darkblue";
        } else if (count <= 900) {
            return index < (count - 800) ? "#39135f" : "#9c66d2";
        } else if (count <= 1000) {
            return index < (count - 900) ? "black" : "#39135f";
        } else {
            return "transparent";
        }
    }
</script>

<meta name="viewport" 
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0"/>

<div class="game-grid">
    <!-- Screen Section (Left Margin) -->
    <div class="screens-section">
        {#each screenEmojis.emojis as emoji}
            <div  
                class="screen" 
                style="background-color: {emoji.backgroundColor}">
                📺
            </div>
        {/each}
    </div>

    <!-- Game Content Section -->
    <div class="game-container">
        <!-- Game Stats Section -->
        <div class="stats">
            <h1 title="here" use:tooltip>Brain Rot Clicker</h1>
            <p>Perceived Rot: {formatNumber(user.totalRot)}</p>
            <p>Screens Owned: {user.screens}</p>
            <p>Rotcoins: ${formatNumber(user.rotCoins)}</p>
            {#if user.autoTechUnlocked}
                <p>Cult Followers: {user.followers}</p>
                <p>Rot per Second: {formatNumber(user.perSec)}</p>
            {/if}
            
        </div>

        <div style="justify-self: center;">
            <h3 style="display: inline; color: red;">Seek Rot:</h3>
            <button 
                onclick={increment}
                style="display: in-line; height: 128px; width: 256px; color: white; background-color: green; border-radius: 0.5rem;">
                Next Video
            </button>
        </div>

        <!-- Clicker Shop (First Shop) -->
        <h2 style="align-self: center; justify-self: center; text-align: center;">Clicker Shop</h2>
        <div class="shop">
            <div class="shop-item">
                <p>Extra Phone Screen: </p>
                <Tooltip title="$1 Rot/click">
                    <button 
                    class={user.rotCoins >= user.screenCost ? "can-afford" : "cant-afford"}
                    onclick={buyScreen}
                    style="width: 100%; min-height: 64px;">
                    ${formatNumber(user.screenCost)} Rotcoins
                    </button>
                </Tooltip>
            </div>

            {#if user.screens >= 10}
                <div class="shop-item">
                    <p>Extra Monitor Screen: </p>
                    <Tooltip title="$10 Rot/click">
                        <button 
                        class={user.rotCoins >= user.x10Cost ? "can-afford" : "cant-afford"}
                        onclick={buyX10}
                        style="width: 100%; min-height: 64px;">
                        ${formatNumber(user.x10Cost)} Rotcoins
                        </button>
                    </Tooltip>
                </div>
            {/if}

            <!-- Unlock Auto-Rot Technologies -->
            {#if !user.autoTechUnlocked}
                <div class="shop-item" id="auto-tech">
                    <p>Unlock Auto-Rot Technologies:</p>
                    <Tooltip title="Rot Per Second?!?">
                        <button 
                        class={user.rotCoins >= user.autoTechCost ? "can-afford" : "cant-afford"}
                        onclick={buyAutoTech}
                        style="width: 100%; min-height: 64px;">
                        ${formatNumber(user.autoTechCost)} Rotcoins
                        </button>
                    </Tooltip>
                </div>
            {/if}
        </div>

        <!-- Idle Tech Shop (Only Available After Unlocking Auto-Rot Technologies) -->
        {#if user.autoTechUnlocked}
            <div class="tech-upgrades">
                <h2 style="justify-self: center;">Auto-Rot Technologies</h2>
                <div class="shop">
                    <div class="shop-item">
                        <p>Rot Cult Follower:</p>
                        <Tooltip title="$25 Rot/second">
                            <button 
                            class={user.rotCoins >= user.followerCost ? "can-afford" : "cant-afford"}
                            onclick={buyFollower}
                            style="width: 100%; min-height: 64px;">
                            ${formatNumber(user.followerCost)} Rotcoins
                            </button>
                        </Tooltip>
                    </div>
                    <div class="shop-item">
                        <p>Rot Cult Legion:</p>
                        <Tooltip title="$250 Rot/second">
                            <button 
                            class={user.rotCoins >= user.legionCostCost ? "can-afford" : "cant-afford"}
                            onclick={buyLegion}
                            style="width: 100%; min-height: 64px;">
                            ${formatNumber(user.legionCost)} Rotcoins
                            </button>
                        </Tooltip>
                    </div>
                </div>
                
            </div>
        {/if}
    </div>

    <!-- Cult Followers Section (Right Margin) -->
    <div class="followers-section">
        {#each followerEmojis.emojis as follower}
            <div 
                class="follower" 
                style="background-color: {follower.backgroundColor}">
                🧑‍💻
            </div>
        {/each}
    </div>

    <!-- Reset Button -->
    <div class="reset">
        <button onclick={resetGame}>Reset Game</button>
    </div>
</div>


<style>
    .game-grid {
        display: grid;
        grid-template-columns: 1fr 3fr 1fr; /* Three columns: left (screens), middle (game content), right (followers) */
        gap: 20px;
        width: 85dvw;
        height: 90dvh;
        margin: 0 auto;
        padding: 20px;
        align-items: start;
    }

    .screens-section, .followers-section {
        display: grid;
        grid-template-columns: repeat(5, 1fr); /* 5 columns */
        grid-template-rows: repeat(20, 1fr); /* 20 rows */
        grid-gap: 4px;
        padding: 10px;
        height: 100%; /* Match the height of the game-container */
    }

    .screen, .follower {
        width: 40px;
        height: 40px;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 5px;
    }

    .game-container {
        font-family: 'Arial', sans-serif;
        padding: 20px;
        background-color: #f4f4f4;
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        height: 100%; /* Full height of the container */
    }

    .stats {
        margin-bottom: 20px;
    }

    .reset {
        margin-top: 20px;
        grid-column-start: 2;
        grid-column-end: 2;
        justify-self: center;
        align-self: center;
    }

    .reset button {
        background-color: #f44336; /* Red */
        color: white;
        font-size: 18px;
        justify-self: end;
        margin-top: 16px;
    }

    .shop {
        display: flex;
        grid-template-columns: 1fr 1fr;
        border: 4px black solid; border-radius: 0.5rem;
        gap: 8px;
    }

    .shop-item {
        margin: 8px;
        display: f;
    }

    #auto-tech {
        align-self: flex-end;
    }
</style>
