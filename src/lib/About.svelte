<script lang="ts">
    // Export a prop to control the visibility from the parent component
    export let isVisible = false;

    function closeModal() {
        isVisible = false;
    }
</script>

{#if isVisible}
<div class="modal-backdrop">
    <div class="modal-content">
        <button class="close-button" on:click={closeModal}>×</button>
        <h2>About Page</h2>
        <p>This is an About page modal dropdown component made with Svelte.</p>
        <!-- Add more about content here -->
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
