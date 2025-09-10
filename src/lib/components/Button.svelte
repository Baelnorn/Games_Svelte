<script>
    let props = $props();
</script>

<!-- Button.svelte -->

<button {...props}>{props.text}</button>

<style>
    button {
        padding: 0.5rem 1rem;
        background-color: var(--color-accent);
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        font-size: 1rem;
        display: block;
        width: 100%;
        margin-top: 0.5rem;
        transition: var(--transition-slow);
        &:hover {
            background-color: var(--color-accent2);
            transition: var(--transition-fast);
        }
    }
</style>