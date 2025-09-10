<script>
  import GameForm from "$lib/GameForm.svelte";
  import { page } from "$app/stores";
</script>

<GameForm id={$page.params.id} />
