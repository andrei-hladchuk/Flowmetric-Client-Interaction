<script>
    import { onMount, createEventDispatcher } from "svelte";
    import ValidatedInput from "$src/base_components/forms/validatedInput.svelte";
    import Swal from "sweetalert2";

    export let modalTitle = "Quick Action";
    export let openOnLoad = false;
    export let type = "text";
    export let inputLabel = "";
    export let inputValue = "";
    export let inputPlaceholder = "";
    export let buttonText = "Add";
    export let buttonSubmittingText = "Adding...";
    export let introText = "";
    export let validateAs = "required";
    export let introClasses = "alert-success"; // Can include any classes - but expects at least alert-[theme-color]
    export let buttonClasses = "btn-primary"; // Can include any classes - but expects at least btn-[theme-color]
    export let mustConfirm = false;
    export let swalConfig = {
        customClass: {
            popup: "dark:bg-red-100",
            icon: "p-1 my-1",
            confirmButton: "btn btn-error ml-2",
            cancelButton: "btn btn-info",
        },
        padding: "1rem",
        buttonsStyling: false,
        iconColor: "#FF1717",
        icon: "error",
        html: "Are you sure?",
        confirmButtonText: "OK",
        timer: 5000,
        showCancelButton: true,
        reverseButtons: true,
    };

    let quickActionInput;

    let isOpen = false;

    export let isSubmitting = false;

    export const toggleModal = (forceOpen = false, resetValidation = true, resetValue = true) => {
        isOpen = !isOpen;
        if (forceOpen) {
            isOpen = true;
        }

        if (resetValidation) {
            quickActionInput.resetValidation();
        }

        if (resetValue) {
            inputValue = "";
        }

        if (isOpen) {
            isSubmitting = false;
            setTimeout(() => {
                quickActionInput.focus();
            }, 200);
        }
    };

    onMount(() => {
        if (openOnLoad) {
            toggleModal(true);
        }
    });

    const validateForm = () => {
        return quickActionInput.validate(true);
    };

    const dispatch = createEventDispatcher();
    const submit = async () => {
        if (!validateForm()) {
            isSubmitting = false;
            return;
        }

        if (!mustConfirm) {
            isSubmitting = true;
            dispatch("submitted");
            return;
        }

        const result = await Swal.fire(swalConfig);

        if (result.isConfirmed) {
            isSubmitting = true;
            dispatch("submitted");
        }
    };

    $: keypress = null;
    $: keypress, handleKeypress();
    const handleKeypress = async () => {
        if (keypress === 13) {
            await submit();
        }
    };
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<div
    class="modal"
    class:modal-open={isOpen}
    on:click|self={() => toggleModal()}
    on:keydown={(event) => {
        if (event.key === "Escape") {
            event.preventDefault();
            toggleModal();
        }
    }}>
    <div class="modal-box relative">
        <button
            on:click={() => {
                toggleModal(false);
            }}
            class="btn btn-link btn-sm absolute right-2 top-2 text-base-content"
            >✕
        </button>
        <h3 class="text-lg font-bold">{modalTitle}</h3>
        {#if introText.length > 0}
            <div class="alert {introClasses} mt-2">{@html introText}</div>
        {/if}
        <div class="form-control mt-2">
            <h1>Snooze by</h1>
            <div style="display: flex;" >
                <button class="btn" style="width: 100px">
                    10 MIN
                </button>
                <button class="btn"   style="width: 100px">
                    30 MIN
                </button>
                <button class="btn" style="width: 100px">
                    60 MIN
                </button>
                <button class="btn" style="width: 100px">
                    90 MIN
                </button>
            </div>
            <div style="display: flex;" >
                <button class="btn" style="width: 200px">
                    THIS AFTERNOON 16:00
                </button>
                <button class="btn" style="width: 200px">
                    TOMORROW 08:00
                </button>
            </div>
            <div style="display: flex;" >
                <button class="btn" style="width: 100px">
                    CUSTOM
                </button>
            </div>
            <h1>Reason</h1>
            <div style="display: flex;" >
                <button class="btn" style="width: 160px">
                    NO ANSWER
                </button>
                <button class="btn" style="width: 160px">
                    CALL BACK LATER
                </button>
                <button class="btn" style="width: 120px">
                    OTHER
                </button>
            </div>
            <button on:click={async () => await submit()} class="btn {buttonClasses}" class:loading={isSubmitting}>
                {#if !isSubmitting}
                    {buttonText}
                {:else}
                    {buttonSubmittingText}
                {/if}
            </button>
        </div>
        <div class="form-control mt-2" style="display: none">
            <h1>Snooze by</h1>
            <ValidatedInput
                style="display: none"
                value="10 MIN"
                label={inputLabel}
                {validateAs}
                {type}
                bind:this={quickActionInput}
                bind:keypress
                validationMessage="Required"
                name="Quick Action Input"
                placeholder={inputPlaceholder}/>
            <h1>Reason</h1>
            <button on:click={async () => await submit()} class="btn {buttonClasses}" class:loading={isSubmitting}>
                {#if !isSubmitting}
                    {buttonText}
                {:else}
                    {buttonSubmittingText}
                {/if}
            </button>
        </div>
    </div>
</div>
