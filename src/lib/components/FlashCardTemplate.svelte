<script>
    import {onMount} from "svelte";

    export let title = "";
    export let id = "";
    export let terms = [];

    const localStorageName = id + '-lesson'

    const cardStudyAmount = 10;

    // Clone the JSON list so we can add additional values to it
    let allFlashCards = terms;

    // This will be a list of 10 terms for the user to learn. It will change with each level
    let studyList = [];

    // Dialog controls
    let isDialogOpen = false;
    let dialogDefinitionReady = false;

    // Opening the dialog
    const handleClick = (term, index) => {
        // Reset each time a new lesson is clicked
        studyList = [];

        // Grab the latest 10 cards. Subtract 1 to get the index
        if(index < cardStudyAmount - 1) {
            // Only grab the cards the user has completed. Slice doesn't use index so add 1
            studyList = allFlashCards.slice(0, index + 1)
        } else {
            // Get the previous 10 cards
            studyList = allFlashCards.slice(index - cardStudyAmount, index)
        }

        dialogDefinitionReady = false;
        isDialogOpen = true;
    }

    const handleClose = () => {
        isDialogOpen = false;
    }

    const handleTermShow = () => {
        dialogDefinitionReady = true;
    }

    const handleTermSuccess = () => {
        dialogDefinitionReady = false;
        studyList.shift();
        studyList = [...studyList];
    }

    const handleTermFailure = () => {
        dialogDefinitionReady = false;
        const missedTerm = studyList.shift();
        studyList = [...studyList, missedTerm];
    }

    const handleSaveLesson = (index) => {
        localStorage.setItem(localStorageName, index);

        updateLevels()
    }

    const updateLevels = () => {
        const currentSavedLesson = localStorage.getItem(localStorageName)

        // Check if new user
        if(currentSavedLesson === null) {
            localStorage.setItem(localStorageName, null);
        }

        // Check if it is valid
        if(currentSavedLesson !== null) {
            const lesson = Number(currentSavedLesson);
            allFlashCards = allFlashCards.map((item, index) => {
                return {
                    ...item,
                    completed: !!(currentSavedLesson >= index)
                }
            })
        }
    }

    onMount(() => {
        updateLevels()
    })
</script>

<div class="container-sm">
    <h1>{title}</h1>

    <div class="row">
        {#each allFlashCards as term, index}
            <div class="item">
                <button type="button" on:click={() => handleClick(term, index)}>
                    <span>Lesson {index}</span>
                </button>

                <label>
                    Complete
                    <input type="checkbox" checked={term.completed} on:change={() => handleSaveLesson(index)} />
                </label>
            </div>
        {/each}
    </div>

    {#if isDialogOpen}
        <div class="dialog-backdrop" on:click={handleClose}></div>

        <dialog open>
            {#if studyList.length === 0}
                <h2>Finished!</h2>

                <button type="button" on:click={handleClose}>Close</button>
            {/if}

            {#if studyList.length > 0}
                <h2>{studyList[0].word}</h2>
                <p>{dialogDefinitionReady ? studyList[0].definition : ''}</p>

                <div class="dialog-controls">
                    <button type="button" disabled={dialogDefinitionReady} on:click={handleTermShow}>Show</button>
                    <button type="button" disabled={!dialogDefinitionReady} on:click={handleTermSuccess}>Success</button>
                    <button type="button" disabled={!dialogDefinitionReady} on:click={handleTermFailure}>Failed</button>
                    <button type="button" on:click={handleClose}>Close</button>
                </div>
            {/if}
        </dialog>
    {/if}
</div>

<style>
    .row {
        display: grid;
        grid-template: "col col col";
        grid-gap: 24px;
    }

    .item {
        text-align: center;
    }

    .item button {
        display: block;
        width: 100%;
        padding: 24px 8px;
        cursor: pointer;
        user-select: none;
        font-weight: 700;
        font-family: monospace;
    }

    .item label {
        display: flex;
        align-items: center;
        justify-content: center;
        font-family: sans-serif;
        padding: 4px;
    }

    dialog {
        position: fixed;
        z-index: 100;
        top: 40vh;
        left: 0;
    }

    .dialog-backdrop {
        content: "";
        background-color: rgba(0, 0, 0, 0.5);
        position: fixed;
        top: 0;
        left: 0;
        z-index: 99;
        width: 100%;
        height: 100vh;
    }

    .dialog-controls button {
        user-select: none;
    }
</style>