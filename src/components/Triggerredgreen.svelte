<script>

import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente
    let data = {
      name: "Trigger",
      text: "Ruft mit Fetch API POST Request die Particle.function 'redgreen' auf, welche die entsprechenden LEDs schaltet.",
}

const DEVICE_ID = "2c0030000447343337373739";
const AUTH_TOKEN = "906d5e4a9041e4c0773cad80ccf23490fe83e76c";

let value = "none";
let items = ["red", "green", "both", "none"];

export let bezeichnung = "Farbe: "; //als Beispiel, wie man der Komponente Attribute mitgeben kann

function triggerLEDs() {
    
    //POST (um Particle.function aufzurufen)
    let funktion = "redgreen";
    console.log(value);
    
    fetch("https://api.particle.io/v1/devices/" + DEVICE_ID + "/" + funktion, {
      body: "access_token=" + AUTH_TOKEN + "&args=" + value,
      headers: {"Content-Type": "application/x-www-form-urlencoded"},
      method: "POST"
    })
      .then(res => {
        if (!res.ok) {
          throw new Error("Failed!");
        }
        console.log(res);
      })
      .catch(err => {
        console.log(err);
      });
  }

</script>

<div class="myCardStyle">  
  <h1>LED Trigger</h1> 
  <h3 class="info" on:click={() => {dispatch("showModalforTrigger", data)}}>INFO</h3>
    <h3 id="text">{bezeichnung}</h3>
    <select bind:value name="farbe">
        {#each items as item}
            <option value={item}>{item}</option> 
        {/each}
    </select>

    <button class="vertikal" on:click={triggerLEDs}>send</button>
</div>

<style>
  .vertikal{
    display: block;
    margin: auto;
  }
    select{
      font-weight: 500;
      font-size: x-large;
      color: black;
      min-width: 200px;
      margin-bottom: 20px;
    }

    button{
      min-width: 100px;
      margin-left: 20px;
      color: white;
      font-size: x-large;
      border-radius: 2em;
      background-color: blue;
      border: 2px solid rgba(255, 62, 0, 0);
      
    }

    button:hover{
      cursor: pointer;
      color: blue;
      background-color: white;
      border: 2px solid blue;
    }

    #text{
      margin-top: -40px;
      font-size: medium;
      font-weight: 300;
      padding-top: 3em;
    }

</style>