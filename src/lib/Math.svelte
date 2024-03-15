<script lang="ts">
    // Export a prop to control the visibility from the parent component
	import Katex from "./Latex.svelte"
    export let isVisible = false;

    function closeModal() {
        isVisible = false;
    }
</script>

{#if isVisible}
<div class="modal-backdrop">
    <div class="modal-content">
        <button class="close-button" on:click={closeModal}>×</button>
         
        <h3>Math Explained</h3>
        <div>
            An algo tree uses recurrence relations to generate a graph of nodes representing the value of
            each call. The nodes are stored in a DFS ordering where each is assigned a length based on   
            the magnitude of the input. The tree is constructed by traversing
            the nodes and drawing lines from point to point. Some extra metadata, namely
            height and row index (from right to left at height h,) are tracked to assist
            in drawing the tree.
              
            <br>
            <br>
            Each node object has inside it a Point object; an x,y pair denoting
            the starting position of the branch. Each Point object is initialized
            using its parent Point as a basis and applying the following formula.
            <br>
            <br> 
            Given node with uninitialized point P let <Katex math={'L'}/> be the 
            length and <Katex math={'i'}/> be the row index and <Katex math={'c'}/>
            is the branching factor. And <Katex math={'P = ({x, y})'}/> is the parent point.
            
           
            <br>
            <br>
            <Katex math={'P\' = ({x\', y\'})'}/>
            <br>
            <br>
            
            <Katex math={'\\theta = \\frac{i \\pi}{\\text{c} + 1}'}/>
            <br>
            <br>
            <Katex math={'x\'= x + cos(\\theta) * l'}/>
            <br>
            <br>
            <Katex math={'y\'= y + sin(\\theta) * l'}/>
            
        </div> 
    </div>
</div>
{/if}

<style>
    .modal-backdrop {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.5);
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .modal-content {
        background-color: white;
        padding: 20px;
        max-width: 50%;
        max-height: 600px;
        overflow-y: scroll;
        border-radius: 8px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }
    .close-button {
        float: right;
        border: none;
        background: none;
        font-size: large;
        cursor: pointer;
    }
</style>

