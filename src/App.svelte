<script>

import MainPage from "./lib/MainPage.svelte";
import Loading from "./lib/Loading.svelte";
import Captcha from "./lib/Captcha.svelte";

let selected = MainPage;
function handleMessage(event){
  if(event.detail.veiw == 1){
    selected = Loading;
  }else if(event.detail.veiw == 2){
    selected = Captcha;
  }
  
}
</script>


<svelte:component this={selected} on:switch={handleMessage}/>