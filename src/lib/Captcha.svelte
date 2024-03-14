<script>
    import { imgData } from "./assets/data.js";

    import { onMount } from "svelte";
    import { fade } from "svelte/transition";

    let mainScore;

    let imgSrc;
    let index = 0;

    let titleSelect = "test";

    let elapsedTime = 0;
    let interval;

    let finalScoreMain = 0;

    let shuffledArr;

    const imageList = [
        "bicycles-1.jpg",
        "bicycles-10.jpg",
        "bicycles-11.jpg",
        "bicycles-12.jpg",
        "bicycles-2.jpg",
        "bicycles-3.jpg",
        "bicycles-4.jpg",
        "bicycles-5.jpg",
        "bicycles-6.jpg",
        "bicycles-7.jpg",
        "bicycles-8.jpg",
        "bicycles-9.jpg",
        "buses-1.jpg",
        "buses-2.jpg",
        "buses-3.jpg",
        "buses-4.jpg",
        "buses-5.jpg",
        "buses-6.jpg",
        "buses-7.jpg",
        "buses-8.jpg",
        "crosswalks-1.jpg",
        "crosswalks-2.jpg",
        "crosswalks-3.jpg",
        "crosswalks-4.jpg",
        "crosswalks-5.jpg",
        "firehydrant-1.jpg",
        "firehydrant-2.jpg",
        "firehydrant-3.jpg",
        "firehydrant-4.jpg",
        "motorcycle-1.jpg",
        "motorcycle-10.jpg",
        "motorcycle-11.jpg",
        "motorcycle-12.jpg",
        "motorcycle-13.jpg",
        "motorcycle-14.jpg",
        "motorcycle-15.jpg",
        "motorcycle-16.jpg",
        "motorcycle-17.jpg",
        "motorcycle-2.jpg",
        "motorcycle-3.jpg",
        "motorcycle-4.jpg",
        "motorcycle-5.jpg",
        "motorcycle-6.jpg",
        "motorcycle-7.jpg",
        "motorcycle-8.jpg",
        "motorcycle-9.jpg",
        "stairs-1.jpg",
        "stairs-2.jpg",
        "stairs-3.jpg",
        "stairs-4.jpg",
        "stairs-5.jpg",
        "trafficlights-1.jpg",
        "trafficlights-10.jpg",
        "trafficlights-11.jpg",
        "trafficlights-12.jpg",
        "trafficlights-13.jpg",
        "trafficlights-14.jpg",
        "trafficlights-15.jpg",
        "trafficlights-2.jpg",
        "trafficlights-3.jpg",
        "trafficlights-4.jpg",
        "trafficlights-5.jpg",
        "trafficlights-6.jpg",
        "trafficlights-7.jpg",
        "trafficlights-8.jpg",
        "trafficlights-9.jpg",
    ];

    onMount(() => {
        // pre load images
        for (let i = 0; i < imageList.length; i++) {
            let img = new Image();
            img.src = `/recaptcha-game/images/${imageList[i]}`;
        }

        shuffledArr = shuffleArray(imageList);

        genRandom();
        interval = setInterval(() => {
            elapsedTime += 1; // Increment the elapsed time by 1 millisecond
        }, 10); // Update every millisecond
        // Cleanup on component unmount
        return () => clearInterval(interval);
    });

    function formatTime(milliseconds) {
        const number = (milliseconds / 100).toFixed(2);
        return `${number}`;
    }

    function handleKeyPress(event) {
        if (event.key === "Enter") {
            // Enter key pressed, handle the action here
            submit();
            // Add your logic here
        }
    }

    function shuffleArray(array) {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
        console.log(array);
        return array;
    }

    let arrindex = 0;

    function genRandom() {

        arrindex++;
        console.log(arrindex)
        imgSrc = shuffledArr[arrindex];
        index = arrindex;
        imgSrc = `./images/${imgSrc}`;
        console.log(imgSrc);
    }

    $: if (imageList[index].includes("bicycles")) titleSelect = "Bicycles";
    $: if (imageList[index].includes("buses")) titleSelect = "Buses";
    $: if (imageList[index].includes("crosswalks")) titleSelect = "Crosswalks";
    $: if (imageList[index].includes("firehydrant"))titleSelect = "Fire Hydrant";
    $: if (imageList[index].includes("motorcycle")) titleSelect = "Motorcycles";
    $: if (imageList[index].includes("stairs")) titleSelect = "Stairs";
    $: if (imageList[index].includes("trafficlights"))titleSelect = "Traffic Lights";

    function news() {
        index++;
    }

    function submit() {
        getVal(this);
    }

    function handleClick() {
        if (
            this.children[0].children[0].classList.contains(
                "rc-image-tile-wrapper-act",
            )
        ) {
            this.children[0].children[0].classList.remove(
                "rc-image-tile-wrapper-act",
            );
            this.children[0].children[1].style.display = "none";
            this.dataset.btnact = "0";
        } else {
            this.children[0].children[0].classList.add(
                "rc-image-tile-wrapper-act",
            );
            this.children[0].children[1].style.display = "block";
            this.dataset.btnact = "1";
        }
    }

    function getVal() {
        let elements = document.querySelectorAll(".rc-imageselect-tile");

        let arr2 = imgData[shuffledArr[index]];

        let test = true;

        let error = 0;
        let total = 0;

        for (let i = 0; i < elements.length; i++) {
            if (arr2[i] === 1) {
                total++;
            }

            if (parseInt(elements[i].dataset.btnact) !== arr2[i]) {
                error++;
                test = false;
            }
        }
        let percent = ((error / total) * 100);
        console.log(percent);

        calculateScore(percent, (elapsedTime / 100).toFixed(2))

        reset();
    }

    //todo fix shitty scoring
    function calculateScore(percentage, timeTakenInSeconds) {
        const maxScore = 500;
        const correctnessExponentialFactor = 0.9; // Exponential factor for correctness component
        const timeExponentialFactor = 4; // Exponential factor for time component
        const timeMultiplier = 0.2; // Multiplier for time component

        // Calculate correctness score non-linearly


        const correctnessScore = maxScore * Math.exp(-correctnessExponentialFactor * percentage);

        // Calculate time score non-linearly
        const timeScore =
            maxScore *
            Math.exp((-timeExponentialFactor * timeTakenInSeconds) / 20); // Exponential decay of score over time

        // Combine correctness score and time score
        const finalScore = (correctnessScore + timeScore) / 2;


        // print(finalScore);
        finalScoreMain += parseInt(finalScore.toFixed(0));
    }


    function reset() {
        elapsedTime = 0;
        let elements = document.querySelectorAll(".rc-imageselect-tile");

        elements.forEach((element) => {
            // Remove class
            element.children[0].children[0].classList.remove(
                "rc-image-tile-wrapper-act",
            );
            // Update style
            element.children[0].children[1].style.display = "none";
            // Update dataset
            element.dataset.btnact = "0";
        });
        genRandom();
    }
</script>

<svelte:head>
    <link href="https://fonts.googleapis.com" rel="preconnect" />
    <link crossorigin href="https://fonts.gstatic.com" rel="preconnect" />
    <link
        href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;900&display=swap"
        rel="stylesheet"
    />
</svelte:head>

<style>
    .mainDiv {
        display: flex;
        width: 100vw;
        height: 100vh;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        background-color: #282c34;
    }

    .text-timer {
        font-size: 32px;
        margin: none;
        padding: none;
        color: white;
        font-weight: bold;
        font-family: Arial, Helvetica, sans-serif;
    }

    .image-select {
        border: 1px solid #cccccc;
        background-color: white;
    }

    .imgt {
        border-collapse: separate;
        border-spacing: 0 0;
        font-family: Roboto, helvetica, arial, sans-serif;
        height: 388px;
        margin: -1px;
        text-align: center;
        transition-delay: 0s;
        transition-duration: 1s;
        transition-property: all;
        transition-timing-function: ease;
        width: 388px;
    }

    .image-select-payload {
        font-family: Roboto, helvetica, arial, sans-serif;
        margin: 0 7px;
        min-width: 240px;
        padding: 7px 0;
    }

    .rc-imageselect-instructions {
        font-family: Roboto, helvetica, arial, sans-serif;
        height: 113px;
        margin-bottom: 7px;
        position: relative;
        width: 386px;
    }

    .rc-imageselect-desc-wrapper {
        background-color: rgb(26, 115, 232);
        color: rgb(255, 255, 255);
        font-family: Roboto, helvetica, arial, sans-serif;
        font-size: 16px;
        height: 66px;
        margin-bottom: 6px;
        padding: 24px;
        position: relative;
    }

    .rc-imageselect-desc-no-canonical {
        color: white;
        font-family: Roboto, helvetica, arial, sans-serif;
        font-size: 16px;
        position: relative;
        font-weight: 400;
    }

    .rc-imageselect-carousel-instructions {
        color: rgb(255, 255, 255);
        display: block;
        font-family: Roboto, helvetica, arial, sans-serif;
        font-size: 14px;
        font-weight: 400;
        opacity: 1;
        transition-delay: 0s;
        transition-duration: 0.2s;
        transition-property: all;
        transition-timing-function: ease;
    }

    .rc-imageselect-tile {
        border-collapse: separate;
        border-spacing: 0 0;
        font-family: Roboto, helvetica, arial, sans-serif;
        margin: 0;
        padding: 1px;
        text-align: center;
        transform: scale(1);
    }

    .rc-imageselect-target {
        border-collapse: separate;
        border-spacing: 0 0;
        font-family: Roboto, helvetica, arial, sans-serif;
        position: relative;
        text-align: center;
    }

    :global(.rc-image-tile-wrapper) {
        border-collapse: separate;
        border-spacing: 0 0;
        font-family: Roboto, helvetica, arial, sans-serif;
        height: 95px;
        overflow-x: hidden;
        overflow-y: hidden;
        position: relative;
        text-align: center;
        transform: matrix(1, 0, 0, 1, 0, 0);
        transition-delay: 0s;
        transition-duration: 0.1s;
        transition-property: all;
        transition-timing-function: ease;
        width: 95px;
    }

    :global(.rc-image-tile-wrapper-act) {
        transform: scale(0.8);
    }

    .rc-imageselect-checkbox {
        background-attachment: scroll;
        background-clip: border-box;
        background-color: rgba(0, 0, 0, 0);
        background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAACXBIWXMAAAsTAAALEwEAmpwYAAAGnmlUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPD94cGFja2V0IGJlZ2luPSLvu78iIGlkPSJXNU0wTXBDZWhpSHpyZVN6TlRjemtjOWQiPz4gPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQWRvYmUgWE1QIENvcmUgNy4xLWMwMDAgNzkuZGFiYWNiYiwgMjAyMS8wNC8xNC0wMDozOTo0NCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczpkYz0iaHR0cDovL3B1cmwub3JnL2RjL2VsZW1lbnRzLzEuMS8iIHhtbG5zOnBob3Rvc2hvcD0iaHR0cDovL25zLmFkb2JlLmNvbS9waG90b3Nob3AvMS4wLyIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0RXZ0PSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VFdmVudCMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIDIzLjAgKE1hY2ludG9zaCkiIHhtcDpDcmVhdGVEYXRlPSIyMDIxLTExLTA0VDIzOjE2OjI2LTA3OjAwIiB4bXA6TW9kaWZ5RGF0ZT0iMjAyMS0xMS0wNFQyMzoxNzozNS0wNzowMCIgeG1wOk1ldGFkYXRhRGF0ZT0iMjAyMS0xMS0wNFQyMzoxNzozNS0wNzowMCIgZGM6Zm9ybWF0PSJpbWFnZS9wbmciIHBob3Rvc2hvcDpDb2xvck1vZGU9IjMiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6NDM3Y2M2MTEtMjg5Mi00MmFkLWEyYmYtMjk1MzA4NGYxNjA1IiB4bXBNTTpEb2N1bWVudElEPSJhZG9iZTpkb2NpZDpwaG90b3Nob3A6YjEwZGYyNmItNGU5Mi0wNTQxLThjMDYtMTJjNWQ5ZDFmMjcxIiB4bXBNTTpPcmlnaW5hbERvY3VtZW50SUQ9InhtcC5kaWQ6ZjE0YzAyYmQtNDJhOC00ODkxLWIxMjMtMWZhYjg2NzZlNzJmIj4gPHhtcE1NOkhpc3Rvcnk+IDxyZGY6U2VxPiA8cmRmOmxpIHN0RXZ0OmFjdGlvbj0iY3JlYXRlZCIgc3RFdnQ6aW5zdGFuY2VJRD0ieG1wLmlpZDpmMTRjMDJiZC00MmE4LTQ4OTEtYjEyMy0xZmFiODY3NmU3MmYiIHN0RXZ0OndoZW49IjIwMjEtMTEtMDRUMjM6MTY6MjYtMDc6MDAiIHN0RXZ0OnNvZnR3YXJlQWdlbnQ9IkFkb2JlIFBob3Rvc2hvcCAyMy4wIChNYWNpbnRvc2gpIi8+IDxyZGY6bGkgc3RFdnQ6YWN0aW9uPSJzYXZlZCIgc3RFdnQ6aW5zdGFuY2VJRD0ieG1wLmlpZDpjMDJkMDg2Zi1mNmZjLTRjMzItYWU2Zi0wOWMxZmU4MzFhNzciIHN0RXZ0OndoZW49IjIwMjEtMTEtMDRUMjM6MTc6MDktMDc6MDAiIHN0RXZ0OnNvZnR3YXJlQWdlbnQ9IkFkb2JlIFBob3Rvc2hvcCAyMy4wIChNYWNpbnRvc2gpIiBzdEV2dDpjaGFuZ2VkPSIvIi8+IDxyZGY6bGkgc3RFdnQ6YWN0aW9uPSJzYXZlZCIgc3RFdnQ6aW5zdGFuY2VJRD0ieG1wLmlpZDo0MzdjYzYxMS0yODkyLTQyYWQtYTJiZi0yOTUzMDg0ZjE2MDUiIHN0RXZ0OndoZW49IjIwMjEtMTEtMDRUMjM6MTc6MzUtMDc6MDAiIHN0RXZ0OnNvZnR3YXJlQWdlbnQ9IkFkb2JlIFBob3Rvc2hvcCAyMy4wIChNYWNpbnRvc2gpIiBzdEV2dDpjaGFuZ2VkPSIvIi8+IDwvcmRmOlNlcT4gPC94bXBNTTpIaXN0b3J5PiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PlXsutAAAASdSURBVFiFtZdbbBRVGIC/mdnZdndr2+0l0BYsaEJ4ABGkUhHjlT6oT7QRbcHaqGkhvFBjBU3AKy9aTYwaQ9pI6wMxEClQiLcmKomtYikCkZsILbYGWuh2aXfLtrvHh53ZPTvdbbdtPMnJzOzMme87/392zjkKyRdFOipx7gupJlWEENiSBCuACij3bfgmx5G+dGUQez4h3S1CwQGh+PoGvcd+u7C3fAgITUcmXk/igbVVNdcrbJqr6nLqrTWJGiz03/FDaHxwd/vueYeAoEVmQhFCJBSIgIuqLxT3pqX/NFVPYkVc7UHl/JaOT1eesYgkJaAAGmArfvnqxp50ffd04HJZMDJS9svnd7cC4/EkhBCoieAP1HRXzgYOcMXl2l+8+exTgI4xhqzPKJZzFdDvf6lz9T8ZBW2zgcsl33N6xe+Na//EEglrBMK9z8qyzxZecY+bLytzIte6fdVHhKOgYYmCKWD23lZUdrxiNvANy9y8W67z2FKNPRvDEt1O38PF1Z0lhkBMKmQBDdB1LadqpvCNy9y885yO3aYwMio4+Ot45J6iFlYRZyyYFyqgLSjZlXfF6S+aCfz5e928bcCHRwV1zQEOXPBE7nc7x592Fj7kwpIGVTpqcwufKJ4p/K1no/DXmgK0SHCzLH70jWVY0mCehFNgc+TLDdbkZtCxLZeaVe6E8MrlUfgtv6CuKUDLxYlwAJuWMY9oBGIEVEBDOHPkBjvK7czPUXm9VGdTHIkXlrt5c30s/GACeNjAkS1FAOQTQEH1euXn61vGuDks0FSF7aU6m4qjElUr3Ow04F4DfuivSeCACHq9Uu8VIDIbKoAyFhi6hiMv0uDbbg+iMZP6F+1kpSlsX6ej4MYfgJ3rdXQtCj88BRzgtvff69bf5AiI/vNHTlkf+O6qh9rGADduhSOxbZ0ehfsEdXuSgwP83fHJ2UQCAhA9x97vi9fw+6setjYEGDAkTPirTQEOX0oOXjgifh7uOebDMj2rxkXIqMF5Q5d2xXtBW6+HWkNiyCd45YsArUnCAfy+E18TOxcIiA4GHUgF0uxpc7Jyqv84nehFjxdkkmKHo5eThwP01c9dDHiBYWAUGBdCCDMFIcNuLDB8zZfV07Y50Yvaej3ThrvO7i0HAsAYlhlRTkHQeCBwZl/FjwVDlz6eFiVBmdN/YsfFo1u7gNvG+2MWJvIgNAVGAf/xhgcb5nsvfzYb+NyBk+91NT95APBLAkFZQJ6bVcLfhVTACbgA15Ky5rU3C0s+mC48/dz+6nNHtrQDI4TzbkqYKZiwJoysiIAUQ8IJOFPdCzOXlDaV92UsqpkKnHfj5Ienvnpm35jfOwz4jCpHIGZFZF2jRdYFhkQq4DCOqYB+5+raRdl3PbJET8nO1VRH+nhoZHBsdGCg/1xrV29nYzfhwXbbgPoJp9TseUz4E62KVaLpsBsiZrUTXVrJX9HIv0gSMKs58mPgpkC8nZF1ZxP5dxhSkwmYEnJNuC+AqXdGZjS0ONX8iMmiZjV7POOdUTwRWUieUuVomZ90wSS9nq6ALCLvkM2jCZGPSe2QhRAoQiS9m/5fyn/lu/UIgBExrQAAAABJRU5ErkJggg==");
        background-origin: padding-box;
        background-position-x: 0;
        background-position-y: 0;
        background-repeat: no-repeat;
        background-size: auto;
        border-collapse: separate;
        border-spacing: 0 0;
        bottom: 0;
        display: block;
        font-family: Roboto, helvetica, arial, sans-serif;
        left: 0;
        position: absolute;
        right: 0;
        text-align: center;
        top: 0;
        pointer-events: none;
        user-select: none;
    }

    .rc-image-tile-44 {
        backface-visibility: hidden;
        border-collapse: separate;
        border-spacing: 0 0;
        font-family: Roboto, helvetica, arial, sans-serif;
        height: 380px;
        left: 0;
        position: relative;
        text-align: center;
        top: 0;
        width: 380px;
        pointer-events: none;
        user-select: none;
    }

    .rc-controls {
        width: 400px;
    }

    .primary-controls {
        height: 60px;
        font-family: Roboto, helvetica, arial, sans-serif;
        border-top: 1px solid #cccccc;
    }

    .verify-button-holder {
        float: right;
        font-family: Roboto, helvetica, arial, sans-serif;
        margin: 9px 8px 9px 0;
    }

    .rc-button-default {
        background-attachment: scroll;
        background-clip: border-box;
        background-color: rgb(26, 115, 232);
        background-image: none;
        background-origin: padding-box;
        background-position-x: 0;
        background-position-y: 0;
        background-repeat: repeat;
        background-size: auto;
        border-bottom-color: rgb(255, 255, 255);
        border-bottom-style: none;
        border-bottom-width: 0;
        border-image-repeat: stretch;
        border-image-source: none;
        border-image-width: 1;
        border-left-color: rgb(255, 255, 255);
        border-left-style: none;
        border-left-width: 0;
        border-right-color: rgb(255, 255, 255);
        border-right-style: none;
        border-right-width: 0;
        border-top-color: rgb(255, 255, 255);
        border-radius: 2px;
        border-top-style: none;
        border-top-width: 0;
        color: rgb(255, 255, 255);
        cursor: pointer;
        display: inline-block;
        font-family: Roboto, helvetica, arial, sans-serif;
        font-size: 14px;
        font-weight: 400;
        height: 42px;
        line-height: 42px;
        min-width: 100px;
        padding: 0 10px;
        position: relative;
        text-align: center;
        text-transform: uppercase;
        transition-delay: 0s;
        transition-duration: 0.5s;
        transition-property: all;
        transition-timing-function: ease;
    }

    .goog-inline-block {
        position: relative;
        display: -moz-inline-box;
        display: inline-block;
    }

    .button-holder {
        float: left;
        font-family: Roboto, helvetica, arial, sans-serif;
        height: 48px;
    }

    .rc-buttons {
        background-repeat: no-repeat;
        float: left;
        font-family: Roboto, helvetica, arial, sans-serif;
        height: 48px;
        margin: 6px 0 6px 6px;
    }

    .rc-button-reload {
        background-attachment: scroll;
        background-clip: border-box;
        background-color: rgba(0, 0, 0, 0);
        background-image: url("https://www.gstatic.com/recaptcha/api2/refresh_2x.png");
        background-origin: padding-box;
        background-position-x: 50%;
        background-position-y: 50%;
        background-repeat: no-repeat;
        background-size: 32px 32px;
        border-bottom-color: rgb(0, 0, 0);
        border-bottom-style: none;
        border-bottom-width: 0;
        border-image-outset: 0;
        border-image-repeat: stretch;
        border-image-source: none;
        border-left-color: rgb(0, 0, 0);
        border-left-style: none;
        border-left-width: 0;
        border-right-color: rgb(0, 0, 0);
        border-right-style: none;
        border-right-width: 0;
        border-top-color: rgb(0, 0, 0);
        border-top-style: none;
        border-top-width: 0;
        cursor: pointer;
        display: inline-block;
        height: 48px;
        opacity: 0.55;
        outline: rgb(0, 0, 0) none 0;
        padding: 0;
        position: relative;
        width: 48px;
    }

    .rc-button:hover {
        opacity: 0.8;
        outline: none;
    }

    .rc-button-audio {
        background-attachment: scroll;
        background-clip: border-box;
        background-color: rgba(0, 0, 0, 0);
        background-image: url("https://www.gstatic.com/recaptcha/api2/audio_2x.png");
        background-origin: padding-box;
        background-position-x: 50%;
        background-position-y: 50%;
        background-repeat: no-repeat;
        background-size: 32px 32px;
        border-bottom-color: rgb(0, 0, 0);
        border-bottom-style: none;
        border-bottom-width: 0;
        border-image-repeat: stretch;
        border-image-source: none;
        border-image-width: 1;
        border-left-color: rgb(0, 0, 0);
        border-left-style: none;
        border-left-width: 0;
        border-right-color: rgb(0, 0, 0);
        border-right-style: none;
        border-right-width: 0;
        border-top-color: rgb(0, 0, 0);
        border-top-style: none;
        border-top-width: 0;
        cursor: pointer;
        display: inline-block;
        height: 48px;
        opacity: 0.55;
        outline: rgb(0, 0, 0) none 0;
        padding: 0;
        position: relative;
        width: 48px;
    }

    .rc-button-help {
        background-attachment: scroll;
        background-clip: border-box;
        background-color: rgba(0, 0, 0, 0);
        background-image: url("https://www.gstatic.com/recaptcha/api2/info_2x.png");
        background-origin: padding-box;
        background-position-x: 50%;
        background-position-y: 50%;
        background-repeat: no-repeat;
        background-size: 32px 32px;
        border-bottom-color: rgb(0, 0, 0);
        border-bottom-style: none;
        border-bottom-width: 0;
        border-image-repeat: stretch;
        border-image-source: none;
        border-image-width: 1;
        border-left-color: rgb(0, 0, 0);
        border-left-style: none;
        border-left-width: 0;
        border-right-color: rgb(0, 0, 0);
        border-right-style: none;
        border-right-width: 0;
        border-top-color: rgb(0, 0, 0);
        border-top-style: none;
        border-top-width: 0;
        cursor: pointer;
        display: inline-block;
        height: 48px;
        opacity: 0.55;
        padding: 0 0 0 0;
        position: relative;
        width: 48px;
    }
</style>


<div class="mainDiv" on:keydown={handleKeyPress}>
    <div class="text-timer">{formatTime(elapsedTime)}</div> <div class="text-timer">{finalScoreMain}</div>

    <div class="image-select" in:fade|global={{ delay: 0, duration: 200 }}>
        <div class="image-select-payload">
            <div class=".rc-imageselect-instructions">
                <div class="rc-imageselect-desc-wrapper">
                    <div class="rc-imageselect-desc-no-canonical">
                        Select all squares with
                        <strong style="font-size: 28px; display: block"
                            >{titleSelect}</strong
                        >
                        <!--                    <span class="rc-imageselect-carousel-instructions">If there are none, click skip</span>-->
                    </div>
                </div>
            </div>

            <table class="imgt">
                <tbody>
                    <tr>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="4"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:0%; left: 0%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="5"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:0%; left: -100%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="6"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:0%; left: -200%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="7"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:0%; left: -300%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="8"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-100%; left: 0%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="9"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-100%; left: -100%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="10"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-100%; left: -200%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="11"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-100%; left: -300%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="12"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-200%; left: 0%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="13"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-200%; left: -100%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="14"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-200%; left: -200%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="15"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-200%; left: -300%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="16"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-300%; left: 0%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="17"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-300%; left: -100%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="18"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-300%; left: -200%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                        <td
                            class="rc-imageselect-tile"
                            data-btnact="0"
                            on:click={handleClick}
                            tabindex="19"
                        >
                            <div class="rc-image-tile-target">
                                <div
                                    class="rc-image-tile-wrapper"
                                    style="width: 95px; height: 95px"
                                >
                                    <img
                                        alt=""
                                        class="rc-image-tile-44"
                                        src={imgSrc}
                                        style="top:-300%; left: -300%"
                                    />
                                    <div class="rc-image-tile-overlay"></div>
                                </div>
                                <div
                                    class="rc-imageselect-checkbox"
                                    style="display: none"
                                ></div>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="rc-separator"></div>
        <div class="rc-controls">
            <div class="primary-controls">
                <div class="rc-buttons">
                    <div class="button-holder reload-button-holder">
                        <button
                            class="rc-button goog-inline-block rc-button-reload"
                            id="recaptcha-reload-button"
                            tabindex="3"
                            title="Get a new challenge"
                            value=""
                        ></button>
                    </div>
                    <div class="button-holder audio-button-holder">
                        <button
                            class="rc-button goog-inline-block rc-button-audio"
                            id="recaptcha-audio-button"
                            tabindex="1"
                            title="Get an audio challenge"
                            value=""
                        ></button>
                    </div>
                    <div class="button-holder image-button-holder">
                        <button
                            class="rc-button goog-inline-block rc-button-image"
                            id="recaptcha-image-button"
                            style="display: none;"
                            tabindex="0"
                            title="Get a visual challenge"
                            value=""
                        ></button>
                    </div>
                    <div class="button-holder help-button-holder">
                        <button
                            class="rc-button goog-inline-block rc-button-help"
                            id="recaptcha-help-button"
                            tabindex="2"
                            title="Help"
                            value=""
                        ></button>
                    </div>
                    <div class="button-holder undo-button-holder">
                        <button
                            class="rc-button goog-inline-block rc-button-undo"
                            id="recaptcha-undo-button"
                            style="display: none;"
                            tabindex="0"
                            title="Undo"
                            value=""
                        ></button>
                    </div>
                </div>
                <div class="verify-button-holder">
                    <button
                        class="rc-button-default goog-inline-block"
                        id="recaptcha-verify-button"
                        on:click={submit}
                        tabindex="0"
                        title=""
                        value=""
                        >Verify
                    </button>
                </div>
            </div>
            <div
                class="rc-challenge-help"
                style="display:none"
                tabindex="0"
            ></div>
        </div>
    </div>
</div>