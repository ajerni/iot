<script>
    import {createEventDispatcher} from 'svelte'; //nichts mit Photon zu tun
    const dispatch = createEventDispatcher() //dieser dipatcher sendet events mit data (event.detail) an die Mutter-Komponente

    
    let data = {
    
    }

    export let title = "";
    export const modalTriggerName = "";
    export let yellowtrigger = false;

</script>

<div class="myCardStyle {yellowtrigger ? "yellow" : ""}">

    <h3 id="info" on:click={() => {dispatch("{modalTriggerName}", data)}}>INFO</h3>
    <h2 id="title">{title}</h2>
    <slot/>

</div>

<style>
    #info{
        color:green;
        position:absolut;
        top: 20px;
        left: 20px;
    }
    #title{
        color:hotpink;
    }
    .yellow{
        background: greenyellow;
    }
</style>