<script>
    import {createEventDispatcher} from 'svelte';

    const dispatch = createEventDispatcher();

    function switchVeiw() {
        dispatch('switch', {
            veiw: 1
        });
    }

</script>

<style>
    .main {
        width: 100vw;
        height: 100vh;
        background-color: #282C34;
        display: flex;
        align-items: center;
        justify-content: space-evenly;
        flex-direction: column;
    }

    .inner {
        /* 		background-color: black; */
    }

    .text {
        font-size: 30px;
        font-family: Roboto, sans-serif;
        color: white;
        /* 		margin-bottom: 10px; */
    }

    .start {
        padding: 15px;
        text-align: center;
        background-color: #a341e4;
        border-radius: 5px;
        cursor: pointer;
        color: white;
        font-family: Roboto, sans-serif;
        font-size: 20px
        /* 		font-weight: bold; */

    }

    .start:hover {
        filter: drop-shadow(0 0 6px #a341e4)
    }

    .instruct {
        width: 50%;
        text-align: center;
        font-family: Roboto, sans-serif;
        font-size: 16px;
        color: white;
    }
</style>
<div class="main">
    <div class="text">Fast Captcha</div>

    <div class="instruct">Your objective is to complete the Recaptchas as quickly and accurately as possible. Be
        careful! Selecting the wrong objects will result in a score penalty. The faster and more accurately you complete
        the Recaptchas, the higher your score will be.
    </div>
    <div class="start" on:click={switchVeiw}>Start Game</div>
</div>