<script>
  import { Router, Link, Route } from "svelte-routing";
  export let url = "";
  import Home from './routes/Home.svelte';
  import Particle from './routes/Particle.svelte'
  // import logo from './assets/svelte.png'
</script>

<main>
  <!-- <img src={logo} alt="Svelte Logo" /> -->
  
  <div class="hero-image">
    <div class="hero-text">
      <h2>Andi's Test Drive with the Internet of Things</h2>
      <h3><a href="https://github.com/ajerni/iot">Diese Seite</a>interagiert mit 2 Particle Photon Boards.</h3>
    </div>
  </div>

  <Router url="{url}" >
    <nav class="navclass">
      <Link to="/">Home</Link>
      <Link to="particle">Particle</Link>
    </nav>
    <div>
      <!-- go from most specific to least specific /> -->
      <!-- <Route path="blog" component="{Blog}" /> -->
      <Route path="particle" component="{Particle}"/>
      <Route path="/"><Home /></Route>
    </div>
  </Router>
  

</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
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

.hero-image {
  background-image: url(./assets/iot1.webp);
  background-color: #cccccc;
  height: 300px;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  position: relative;
}

.hero-text {
  text-align: left;
  position: absolute;
  top: 28%;
  left: 57%;
  /* top: 50%;
  left: 50; */
  /* transform: translate(-50%, -50%); */
  color: white;
}

.navclass {
  font-size:2rem; 
  padding-top: 20px; 
}

:global(a) {
      text-decoration: none;
      color: rgb(5, 5, 145);
      padding: 0px 50px;
}

:global(a):hover {
      font-size: 1.8rem;
      color: blue;
}

h3 a, h3 a:active, h3 a:visited, h3 a:hover {
    text-decoration:underline;
    color: white;
    font-size: inherit;
    padding: 0px 5px;
}
    
</style>
