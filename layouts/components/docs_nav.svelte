<script>
    export let allContent, checked;
    const uncheck = () => {
        checked=false;
    }
    let group = [];
    const addGroup = newGroup => {
        group.push(newGroup);
        return newGroup;
    }
    const sortNav = (a, b) => {
        if (typeof a.fields.order !== "undefined" && typeof b.fields.order !== "undefined") {
            return a.fields.order - b.fields.order;
        }
        return -1;
    }
    
    let active = [];
    const setActive = (path) => {
        active = [];
        setTimeout(function(){
            if (path === window.location.pathname) {
                active[path] = true;
            }
        }, 100);
    }

    // Set active on initial page load.
    import {onMount} from 'svelte/internal';
    onMount(async () => {
        let initialPath = await window.location.pathname;
        if (initialPath.length > 1 && initialPath.slice(-1) === "/") {
          initialPath = initialPath.slice(0, -1); // remove trailing slash.
        }
        active[initialPath] = true;
    });
</script>
<div class="sidebar">
    {#each allContent.sort(sortNav) as content (content.path)}
        {#if content.type == "docs" && !content.fields.deprecated}
            {#if !group.includes(content.fields.group)}
                <strong>{addGroup(content.fields.group)}</strong>
            {/if}
            <a on:click={() => setActive("/" + content.path)} on:click={uncheck} class:active={active["/" + content.path]} href="/{content.path}" class="nav-link">{content.fields.title}</a>
        {/if}
    {/each}
</div>

<style>
    .sidebar {
        position: sticky;
        top: 60px;
        border-right: 1px solid var(--base);
        overflow: scroll;
        overflow-x: auto;
        height: 100%;
        max-height: 100vh;
        min-width: 170px;
        padding: 20px;
    }
    .sidebar::-webkit-scrollbar {
        width: 6px;
    }
    .sidebar::-webkit-scrollbar-track {
        background: var(--base-light);
        border-radius: 8px;
    }
    .sidebar::-webkit-scrollbar-thumb {
        background-color: var(--base);
        border-radius: 8px;
    }
    strong, .nav-link {
        display: block;
        margin-bottom: 10px;
        white-space: nowrap;
    }
    a {
        padding: 5px 10px;
    }
    a:hover {
        background: #f7f7f7;
        border-radius: 6px;
    }
    .active {
        color: var(--accent-dark);
    }
</style>
