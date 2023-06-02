<script>

import MainPage from "./lib/MainPage.svelte";
let selected = MainPage;
function handleMessage(){
  alert("test");
}
</script>


<svelte:component this={selected} on:message={handleMessage}/>