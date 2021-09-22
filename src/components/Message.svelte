<script>
    import { onMount } from "svelte";
    import { token } from './token-store.js'

    import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente
    let data = {
      name: "Message",
      text: "Fetch API mit GET Request um auf Particle.variable zuzugreifen und POST Request um Particle.function aufzurufen.",
    }
    
    const DEVICE_ID = "2c0030000447343337373739";
    const AUTH_TOKEN = $token;

    let message = "default value";
    let isLoading = false;
  
    onMount(() => {
      isLoading = true;
      //GET (um auf Particle.variable zuzugreifen)   
      let variable = "messagevar";
      fetch("https://api.particle.io/v1/devices/" + DEVICE_ID + "/" + variable + "?access_token=" + AUTH_TOKEN)
        .then(res => {
          if (!res.ok) {
            throw new Error("Failed!");
          }
          return res.json();
        })
        .then(data => {
          isLoading = false;
          console.log("Aktueller Wert: " + data.result);
          message = data.result;
        })
        .catch(err => {
          isLoading = false;
          console.log(err);
        });
    });
  
    function setNewMessage() {
      isLoading = true;
      //POST (um Particle.function aufzurufen)
      let funktion = "setmessage";
      let argumente = message;
      fetch("https://api.particle.io/v1/devices/" + DEVICE_ID + "/" + funktion, {
        body: "access_token=" + AUTH_TOKEN + "&args=" + argumente,
        headers: {"Content-Type": "application/x-www-form-urlencoded"},
        method: "POST"
      })
        .then(res => {
          isLoading = false;
          if (!res.ok) {
            throw new Error("Failed!");
          }
          console.log(res);
        })
        .catch(err => {
          isLoading = false;
          console.log(err);
        });
    }
  </script>

  <div class="myCardStyle">
    <h1>Message</h1>
    <h3 class="info" on:click={() => {dispatch("showModalforMessage", data)}}>INFO</h3>

    <p>MESSAGE:</p>

    <div class="text">
      {#if isLoading}
        <p class="schrift">Loading from Photon...</p>
      {:else}
        <p class="schrift">{message}</p>
      {/if}

      <label for="message">New:</label>
      <input class="text" type="text" id="messageinput" bind:value={message} />
      <button on:click={setNewMessage}>set</button>
    </div>

  </div>  

  <style>
    button{
      min-width: 100px;
      margin-top: 20px;
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

    .text{
      font-weight: 500;
      font-size: x-large;
      color: blue;
      /* padding-top: 20px; */
    }

    .schrift{
      font-size: medium;
      font-weight: 300;
      /* padding-top: 3em; */
    }
  </style>

