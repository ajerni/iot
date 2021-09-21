<script>
    import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente
    let data = {
      name: "Lichtsensor",
      text: "Die EventSource-Instanz (eventSource.addEventListener) hört auf die Events 'andi_hell' und 'andi_dunkel', welche von Photon A gesendet (Particle.publish) werden, sobald der Lichtsensor einen Schwellwert erreicht. Dies ändert dann über dynamisch zugewisene CSS Klassen die Hintergrundfarbe. Auch Particle B hört auf diese Events (Particle.subscribe) und schaltet entsprechend das LED."
    }

      const DEVICE_ID = "2c0030000447343337373739";
      const AUTH_TOKEN = "906d5e4a9041e4c0773cad80ccf23490fe83e76c";

      let eventSource = new EventSource("https://api.spark.io/v1/devices/" + DEVICE_ID + "/events/?access_token=" + AUTH_TOKEN);
      
      let bgColor = "white";

      eventSource.addEventListener('andi_dunkel', function(e) {
        bgColor = "black";
      }, false);
  
      eventSource.addEventListener('andi_hell', function(e) {
        bgColor = "white";
      }, false);

      

</script>

<div class="myCardStyle {bgColor}">
    <h1>Lichtsensor</h1>
    <h2 id="text">Hintergrund wechselt die Farbe</h2>
    <h3 class="info" on:click={() => {dispatch("showModalforLicht", data)}}>INFO</h3>
    
</div>

<style>
    .black{
        background: greenyellow;
    }
    #text{
        font-size: medium;
        font-weight: 300;
        padding-top: 2em;
  }
</style>