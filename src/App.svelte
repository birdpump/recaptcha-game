<script>

import MainPage from "./lib/MainPage.svelte";
import Loading from "./lib/Loading.svelte";

let selected = MainPage;
function handleMessage(){
  selected = Loading;
  if(selected = Loading){
    alert("next page");
  }
}
</script>


<svelte:component this={selected} on:message={handleMessage}/>