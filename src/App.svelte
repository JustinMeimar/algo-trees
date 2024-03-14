<script lang="ts">
  import Tree from './lib/Tree.svelte'
  import About from './lib/About.svelte'

  let showAbout = false;
  const toggleAboutModal = () => {
    console.log(showAbout);
    showAbout = !showAbout;
  }

</script>

<main>
  <About isVisible={showAbout}></About>
  <Tree></Tree> 
  <div class="footer">
    <button id="about-btn" on:click={toggleAboutModal}>About</button>
    <p>&copy; Justin Meimar 2024</p>
  </div>
</main>

<style> 
  .footer {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    text-align: center;
  }
  #about-btn {
    padding: 5px 10px;
    background-color: #308903;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin: 10px;
  }

  #about-btn:hover {
      background-color: #0056b3;
  }

</style>
