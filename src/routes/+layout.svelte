<svelte:head>
    <link href="/global.css" rel="stylesheet" />
</svelte:head>

<div class="container">
    <header>
        <a href="/">Home</a>
    </header>

    <main>
        <slot></slot>
    </main>
</div>

<style>
    header {
        text-align: right;
    }
    header a {
        display: inline-block;
        padding: 8px 12px;
    }
</style>