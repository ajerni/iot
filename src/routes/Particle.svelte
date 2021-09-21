<script>
    import Codea from '../components/Codea.svelte';
    import Codeb from '../components/Codeb.svelte';

</script>

<h1>Particle code (C++)</h1>

<div class="wrapper">

    <div class="max"><Codea/></div>
    <div class="max"><Codeb/></div>

</div>

<style>
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
        display: grid;
        grid-template-columns: 1.2fr 1fr;
        /* column-gap: 20px; */
    }
    .max{
        max-width: 300px;
        margin: 20px;
    }
    
</style>




