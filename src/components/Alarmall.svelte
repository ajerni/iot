<script>
    import { token } from './token-store.js'
    import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente
    let data = {
      name: "Alarm",
      text: "Der Alarmknopf triggert einen Event (alarmall) ab der Homepage (POST request mit Fetch API) auf den Photon A und Photon B hören (Particle.subscribe)."
    }

    const AUTH_TOKEN = $token;

    function publishEvent(){

        let eventname = "alarmall";
        let daten = "";

        fetch("https://api.particle.io/v1/devices/events", {
            body: "name=" + eventname + "&data=" + daten + "&access_token=" + AUTH_TOKEN,
            headers: {"Content-Type": "application/x-www-form-urlencoded"},
            method: "POST"
        })
    }
</script>

<div class="myCardStyle">
  <h1>Alarm</h1>
  <h3 class="info" on:click={() => {dispatch("showModalforAlarm", data)}}>INFO</h3>
  <button on:click={publishEvent}>Alarm all</button>
</div>

<style>
    button {
      font-family: inherit;
      font-size: x-large;
      font-weight: 500;
      padding: 0.5em 0.5em;
      background-color: white;
      color: blue;
      border-radius: 2em;
      border: 2px solid blue;
      outline: none;
      width: 200px;
      cursor: pointer;
      margin: 20px;
    }

    button:hover{
      color: white;
      background-color: #ff3e00;
      border-radius: 2em;
      border: 2px solid rgba(255, 62, 0, 0);
    }
    
    /* button:focus {
      border: 2px solid #ff3e00;
    }
  
    button:active {
      background-color: rgba(255, 62, 0, 0.2);
    } */
  </style>