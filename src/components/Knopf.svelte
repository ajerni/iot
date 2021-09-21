<script>
    import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente
    let exampleData = {
      name: "Andi",
      age: 44,
      text: "Mail sent via IFTT",
    }
    let data = {
      name: "Knopf auf Particle Photon",
      text: "Ein Knopfdruck auf Photon A trigert den Event 'knopf' (Particle.publish). Die Variable counter (Particle.variable) wird über einen GET Request (Fetch API) ausgelesen, sobald Photon A den Event 'knopf' sendet. Der entsprechende Event Listener auf der Homepage wird auf einer EventSource-Instanz registriert (eventSource.addEventListener())."
    }

    const DEVICE_ID = "2c0030000447343337373739";
    const AUTH_TOKEN = "906d5e4a9041e4c0773cad80ccf23490fe83e76c";

    let counter = 0;
    //nur als Beispiel: die reaktiven variablen und statements ($:) updaten immer automatisch, wenn eine var (auf der rechten Seite) sich ändert
    $: doubled = counter * 2;
    $: {
      console.log("*reactive* doubled counter: " + doubled);
      //...
    }

    let eventSource = new EventSource("https://api.spark.io/v1/devices/" + DEVICE_ID + "/events/?access_token=" + AUTH_TOKEN);
    
    eventSource.addEventListener('knopf', function(e) {

      //GET (um auf Particle.variable zuzugreifen)   
      let variable = "counter";
      fetch("https://api.particle.io/v1/devices/" + DEVICE_ID + "/" + variable + "?access_token=" + AUTH_TOKEN)
        .then(res => {
          if (!res.ok) {
            throw new Error("Failed!");
          }
          return res.json();
        })
        .then(data => {
          console.log("Zählerstand: " + data.result);
          
          //Zuweisung des erhaltenen Resultats (data.result)
          counter = data.result;
          
          //nichts mit Photon zu tun. Beispiel für Kommunikation unter Komponenten in Svelte (exampleData landet in event.detail )
          if(counter==5){
            dispatch("countfive", exampleData);
          }

        })
        .catch(err => {
          console.log(err);
        });
          
    }, false);

    dispatch("sentData", data);

</script>

<div class="myCardStyle">
  <h1>Knopf</h1>
  <h3 class="info" on:click={() => {dispatch("showModalforKnopf", data)}}>INFO</h3>
    <h1 id="counter">Stand: {counter}</h1>
    <div id="slot">
      <slot />
    </div> 
</div>

<style>
  #counter{
    font-weight: 500;
    font-size: x-large;
    color: blue;
    padding-top: 20px;
  }
  #slot{
    font-size: medium;
    font-weight: 300;
    padding-top: 3em;
  }
</style>

