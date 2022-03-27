<script>
    export let name;
    export let download;
</script>

<main class="h-16 p-4 flex items-center justify-between w-full border-2 rounded-lg my-4">
    <p class="h-fit">{name}</p>
    <button class="justify-self-end" on:click={download}><i class="fa fa-download"></i></button>
</main>