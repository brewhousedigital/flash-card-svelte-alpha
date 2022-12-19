<script>
    let appList = [
        {name: "Sogeti Overview", url: "/study/sogeti-overview"},
        {name: "Sogeti Pursuit", url: "/study/sogeti-pursuit"},
    ]
</script>

<h1>Choose an app</h1>

<div class="app-list">
    {#each appList as item}
        <div>
            <h2>{item.name}</h2>
            <a href={item.url} class="stretched-link">Learn!</a>
        </div>
    {/each}
</div>


<style>
    .app-list {
        display: grid;
        grid-gap: 24px;
        grid-template: "col";
    }

    .app-list > div {
        border: 1px solid #eee;
        padding: 30px;
        border-radius: 20px;
        position: relative;
        transition: all 200ms;
    }

    .app-list > div:hover {
        background-color: darkslategray;
    }

    @media screen and (min-width: 768px) {
        .app-list {
            grid-template: "col col col";
        }
    }
</style>