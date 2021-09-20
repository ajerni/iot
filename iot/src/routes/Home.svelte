<script>
  import Message from '../components/Message.svelte'
  import Wcsensor from '../components/Wcsensor.svelte'
  import Alarmall from '../components/Alarmall.svelte'
  import Knopf from '../components/Knopf.svelte'
  import Triggerredgreen from '../components/Triggerredgreen.svelte'
  import Modal from '../components/Modal.svelte'
  import Lichtsensor from '../components/Lichtsensor.svelte'

    const DEVICE_ID = "2c0030000447343337373739";
    const AUTH_TOKEN = "906d5e4a9041e4c0773cad80ccf23490fe83e76c";

    let modaltoggle = false;
    let komponentenName = "";
    let modalContent = "";
    
    let eventSource = new EventSource("https://api.spark.io/v1/devices/" + DEVICE_ID + "/events/?access_token=" + AUTH_TOKEN);

    //let showModal = (event) => {}

    function showModal(event){
      console.log("shows modal when counter reached 5 on Photon");
      console.log(event.detail.name);

      komponentenName = event.detail.name;
      modalContent = event.detail.text;
      
      //show modal
      modaltoggle = true;
    }

    function handleCancel(){
      //close modal
      modaltoggle = false;
    }


</script>





<h1>Andi's IOT Cockpit</h1>

  <div class="wrapper">
    
    <Knopf on:showModalforKnopf={showModal}>Bei Zählerstand 5 geht E-Mail raus über IFTTT</Knopf>
    <Triggerredgreen bezeichnung={"Select color:"} on:showModalforTrigger={showModal}/> 
    <Wcsensor on:showModalforWc={showModal}/> 
    <Message on:showModalforMessage={showModal}/> 
    <Alarmall on:showModalforAlarm={showModal}/> 
    <Lichtsensor on:showModalforLicht={showModal}/> 

    {#if modaltoggle}
      <Modal title={komponentenName} on:cancel={handleCancel}>{modalContent}</Modal>
    {/if}

  </div>

  <style>
    :root {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
        Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    }
  
    h1 {
      color: #ff3e00;
      text-transform: uppercase;
      font-size: 4rem;
      font-weight: 100;
      line-height: 1.1;
      margin: 2rem auto;
      /* max-width: 24rem; */
    }
  
    .wrapper{
      background-color: rgba(49, 136, 243, 0.098);
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      column-gap: 10px;
      row-gap: 1em;
      min-height: 600px;
      border-radius: 8px;
    }
  
    @media only screen and (max-width: 720px) {
      .wrapper{
      display: grid;
      grid-template-columns: 1fr;
      }
    }
  
    :global(.myCardStyle){ /* :global opts out of the scoping */
      position: relative;
      border: 2.6px solid #ff3e00;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
      border-radius: 8px;
      background: white;
      margin: 1rem;    
      padding-bottom: 20px;
      min-height: 250px;
    }
  
    :global(.myCardStyle h1){
      color: #ff3e00;
      font-weight: 300;
    }
  
    :global(.info){
      color:rgb(41, 73, 255);
      position: absolute;
      top: .6em;
      left: 1.5em;
      font-weight: 200;
    }
  
    :global(.info:hover){
     cursor: pointer;
    }
  
  </style>
  