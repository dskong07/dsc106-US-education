<h1 class = 'title'>Welcome to DK and Jill's Project!</h1>
<p class ='greetings'>Visit <a href="https://github.com/dkong07/dsc106-p3/tree/main">the repository</a> for source files, also hi!</p>

<script>
    import App from '../components/App.svelte';
</script>

<App />

<svelte:head>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap">
    <link rel = "stylesheet" href="https://fonts.googleapis.com/css2?family=Kode+Mono:wght@400..700&display=swap">

</svelte:head>

<style>
    .title{
        font-family: "Kode Mono", monospace;
        text-align: center;
        font-size: 40px;
    }
    .greetings{
        font-family: 'Roboto', sans-serif;
        font-size: 25px;
        text-align: center;
    }
</style>

