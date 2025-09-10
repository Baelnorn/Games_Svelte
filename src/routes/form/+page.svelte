<script>
	import { FormInput, Button } from 'components';

	let name = $state('');
	let email = $state('');
	let password = $state('');
	let referrer = $state('');
</script>

<!-- ABOUT -->
<div class="myform">
	<FormInput name="Name" bind:value={name} type="text" labelText="Name" required />
	<FormInput name="Email" bind:value={email} type="email" labelText="Email" required />
	<FormInput name="Password" bind:value={password} type="password" labelText="Password" />
	<FormInput
		name="referrer"
		bind:value={referrer}
		type="text"
		labelText="How did you heard about us?"
		labelAddition="(optional)"
	/>
	<Button type="button" text="Submit" onclick={()=>{alert("test")}}/>
</div>

<style>
	.myform {
		width: min(400px , 90%);
		margin: 2rem auto;
		padding: 1rem;
		border: 1px solid #ccc;
		border-radius: 8px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
		background-color: #f9f9f9;
	}
</style>
