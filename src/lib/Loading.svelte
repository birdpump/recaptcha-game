<script>
    import {tweened} from 'svelte/motion';
    import {fade} from 'svelte/transition';

    import {createEventDispatcher} from 'svelte';

    const dispatch = createEventDispatcher();

    function switchVeiw() {
        dispatch('switch', {
            veiw: 2
        });
    }


    let setTime = 1.8;
    let time = setTime;

    const progress = tweened(1, {
        duration: setTime * 1000
    });
    progress.set(0);
    //TODO implement switch in comp
    $: if ($progress == 0) {
        switchVeiw();
    }
</script>

<style>
    .main {
        width: 100vw;
        height: 100vh;
        background-color: #282C34;
    }

    .wrapper{
        width: 100vw;
        height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
    }

    .text {
        font-size: 30px;
        font-family: Roboto, sans-serif;
        color: white;
        margin-bottom: 15px;
    }

    .prog {
        width: 70%;
        height: 6px;
        background-color: black;
    }

    .inner {
        background-color: #a341e4;
        height: 6px;
        float: left;
    }
</style>

<div class="main">
    <div class="wrapper" in:fade="{{delay: 0, duration: 200}}">
        <div class="text">Get Ready!</div>
        <div class="prog">
            <div class="inner" style="width: {$progress*100}%"></div>
        </div>
    </div>
</div>