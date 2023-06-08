<script>
    import MainNav from "$src/lib/navigation/mainNav.svelte";
    import { push, pop, replace } from "svelte-spa-router";
    import QuickActionsMenu from "$src/lib/navigation/quickActionsMenu.svelte";
    import { checkAuthentication, logout } from "$src/lib/js/stores/authentication";
    import { onMount } from "svelte";
    import PageTransitionFade from "$src/base_components/page_transitions/pageTransitionFade.svelte";
    import QuickActionModal from "$src/quickActionModal.svelte";

    const openComponentLibrary = () => {
        push("/component-library");
    };

    let clientInteractionObject = [];
    let clientInteractionsArray = [];
    let clientInteractionTypeList = [];

    onMount(async () => {
        fetch("http://localhost:3333/clientInteractions/1")
            .then(response => response.json())
            .then(data => {
                clientInteractionObject = [data];

                fetch("http://localhost:3333/clientInteractions")
                    .then(response => response.json())
                    .then(data => {
                        clientInteractionsArray = data;

                        fetch("http://localhost:3333/clientInteractionTypes")
                            .then(response => response.json())
                            .then(data => {
                                clientInteractionTypeList = data;
                    }).catch(error => {
                    return [];
                });
            });             
        }).catch(error => {
            console.log(error);
            return [];
        });
    });

    let quickActionModal;
</script>

<QuickActionModal
    bind:this={quickActionModal}
    modalTitle = "Snooze Interaction"
    mustConfirm={true}
    inputLabel="Label"
    type = "text"
    buttonText = "Save">
</QuickActionModal>

<PageTransitionFade>
    <MainNav />
        <main style="display: flex; align-items: center; flex-direction: column; width: 600px; margin: 0 auto">
            <div style="width: 500px" class="fm-div-bg fm-shadow bg-white rounded-md fm-shadow p-6 w-600" > 
                <h1 class="text-3xl">Client Iteraction</h1>
                <h3>Next Client Iteraction</h3>

                <div style="display: flex; align-items: center; justify-content: space-between;">
                    <input type='time' />
                    <input type='date' />
                    <button on:click={() => {
                        quickActionModal.toggleModal(true);
                    }}>
                        OPEN
                        <svg fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                        </svg>
                    </button>
                </div>

                <div style="padding:2% 0 2% 0; border-top:3px solid grey; border-bottom: 3px solid grey;" class="m-3">
                    <h3>Previous Client Iteraction</h3>
                    {#each clientInteractionObject as item, index}
                        <div class="bg-gray-50 border border-gray-300 text-gray-900 text-sm p-2" style="width: 425px; border-left: 10px solid pink">
                            <h6 class="text-1xl m-1">{item.interactionDateTime}</h6>
                            <h3 class="text-3xl m-1">Online Meeting</h3>
                            <h4 class="text-2xl m-1">{item.description}</h4>
                        </div> 
                    {/each} 
                </div>

                <h1 class="text-3xl">Comments / Notes</h1>
                <input type="text" placeholder="Make a new comment" class="my-3 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" required>
                {#each clientInteractionsArray as item, index}
                    {#each clientInteractionTypeList as type}
                        {#if item.clientInteractionType === type.id}
                            <div class="bg-gray-50 border border-gray-300 text-gray-900 text-sm p-2" style="width: 425px; border-left: 10px solid pink">
                                <h6 class="text-1xl m-1">{item.interactionDateTime}</h6>
                                <h3 class="text-3xl m-1">{type.name}</h3>
                                <h4 class="text-2xl m-1">{item.description}</h4>
                            </div> 
                        {/if}
                    {/each} 
                {/each}
            </div>
        </main>
    <QuickActionsMenu />
</PageTransitionFade>

<style>
    .fm-shadow {
        --tw-shadow: 0 0 5px 1px rgb(0 0 0 / 0.1), 0 0 5px 1px rgb(0 0 0 / 0.1);
        --tw-shadow-colored: 0 0 5px 1px var(--tw-shadow-color), 0 0 5px 1px var(--tw-shadow-color);
        box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000), var(--tw-ring-shadow, 0 0 #0000), var(--tw-shadow);
    }
</style>
