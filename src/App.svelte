<script lang="ts">
  import Tree from './lib/Tree.svelte'
  import Footer from './lib/Footer.svelte'
</script>

<main>
  <div class="title">
    <h1>Algo-Trees</h1>
  </div>
  <Tree></Tree> 
  <p>&copy; Justin Meimar 2024</p>
</main>

<style>
  .title {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
    /* filter: drop-shadow(0 0 2em #169500aa); */
  }
  .title:hover {
    filter: drop-shadow(0 0 1.5em #0d6500aa);
  }
</style>
