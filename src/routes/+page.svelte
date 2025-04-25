<script>
  import { onMount } from "svelte";
  import { prefersReducedMotion } from "svelte/motion";

    let totalRot = $state(0);
    let rotCoins = $state(50000);
    let perSec = $state(0);
    let multi = $state(1);
    let x2 = $state(false);
    let x10 =  $state(false);
    let autoTechUnlocked = $state(false);

    onMount(function welcome() {
        alert("Brain Rot Clicker\n\nbuy more screens, watch more videos, expand your knowledge of brainrot")
    })

    function increment() {
		rotCoins += (1 * multi);
        totalRot += (1 * multi);

	}
    function timer() {
        rotCoins += perSec;
        totalRot += perSec;
    }
    setInterval(timer, 1000);

    function buyField() {
        if (rotCoins >= 50) {
            rotCoins -= 50;
            multi += 1;
            x2 = false;
        } else { 
            x2 = true;
        }

        if (x2) {
            alert("You must have at least $50 for a new screen!");
        }
    }

    function buyX10() {
        if (rotCoins >= 1000) {
            rotCoins -= 1000;
            multi += 10;
            x10 = false;
        } else { 
            x10 = true;
        }

        if (x10) {
            alert("You must have at least $1000 for a new screen!");
        }
    }

    function buyAutoTech() {
        if (rotCoins >= 15000) {
            rotCoins -= 15000;
            autoTechUnlocked = true;
        }

        if ((autoTechUnlocked == false) && (rotCoins < 15000)) {
            alert("You must have at least $15000 to unlock Auto-Rot Technologies!");
        }
    }

    function buyFollower() {
        if (rotCoins >= 15000) {
            perSec += 25;
            rotCoins -= 15000;
        }
    }
</script>


<h1>brain rot clicker</h1>
<div>
    <p>Percieved Rot: {totalRot}</p>
    <p>screens owned: {multi}</p> 
    <p>Rotcoins: ${rotCoins}</p>
    <p>Rot per second: {perSec}</p>
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
        {#if (autoTechUnlocked==false)}
        <button onclick={buyAutoTech}>
            $15000 Rotcoins
        </button>
        {:else}
        <p style="display: inline;">Unlocked</p>
        {/if}
    </div>
    {#if autoTechUnlocked}
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