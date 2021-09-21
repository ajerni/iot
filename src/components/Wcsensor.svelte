<script>

    import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente
    let data = {
      name: "Magnestsensor",
      text: "EventSource (eventSource.addEventListener) hört auf die von Photon B gesendeten (Particle.publish) Events 'offen' und 'zu'. In den Callback-Funktionen wird dann über eine status Variable das gewünschte Element angezeigt.",
    }

    const DEVICE_ID = "2d0040000847343337373738";
    const AUTH_TOKEN = "906d5e4a9041e4c0773cad80ccf23490fe83e76c";

    let status = false;
    
    let eventSource = new EventSource("https://api.spark.io/v1/devices/" + DEVICE_ID + "/events/?access_token=" + AUTH_TOKEN);
    
    eventSource.addEventListener('offen', function(e) {
           status = false;
    }, false);

    eventSource.addEventListener('zu', function(e) {
           status = true;
    }, false);

</script>

<!-- <div class={varxy ? classA : classB}> wäre eine Alternative um CSS Klassen dynamich anzuwenden -->
<div class="myCardStyle">
    <h1>Magnetsensor</h1>
    <h3 class="info" on:click={() => {dispatch("showModalforWc", data)}}>INFO</h3>
    {#if status}
    <h2 id="besetzt">BESETZT</h2>
    {:else}
    <h2 id="frei">FREI</h2>
    {/if}
</div>

<style>
    #besetzt{
        border: 2px solid red;
        color: red;
        max-width: 50%;
        padding: 2em;
        margin: auto;
    }

    #frei{
        border: 2px solid green;
        color: green;
        max-width: 50%;
        padding: 2em;
        margin: auto;
    }
</style>
    