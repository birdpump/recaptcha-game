<script>

import MainPage from "./lib/MainPage.svelte";
import Loading from "./lib/Loading.svelte";
import Captcha from "./lib/Captcha.svelte";

let selected = MainPage;
function handleMessage(){
  if(selected = Loading){
    selected = Captcha;
  }
  selected = Loading;
}
</script>


<svelte:component this={selected} on:switch={handleMessage}/>