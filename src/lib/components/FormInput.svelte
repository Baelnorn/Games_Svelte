<script lang="ts">
    let { name, labelText, required = false, value = $bindable(''), type='text', labelAddition='' } = $props();
</script>

<!-- formInput.svelte -->

{#if labelText}
  <label for={name} {labelAddition}>{labelText}</label>
{/if}
<input {name} id={name} {type} bind:value={value} {required} />

<style>
	input {
		width: 100%;
		padding: 0.5rem;
		margin-bottom: 0.5rem;
		border: 1px solid #ccc;
		border-radius: 4px;
		box-sizing: border-box;
		font-size: 1rem;
	}
	
	label {
		display: block;
		margin: 0.5rem 0 0.25rem 0;
	}

	label:has(+ input:required)::after {
		content: ' *';
		color: #f44336;
	}

	label:has(+ input:optional)::after {
		content: attr(labelAddition);
		color: #6f6f6f;
		margin-left: 0.25rem;
	}
</style>
