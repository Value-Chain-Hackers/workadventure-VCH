<script lang="ts">
    import { ChatMessage } from "../../Connection/ChatConnection";
    import { selectedChatMessageToEdit, selectedChatMessageToReply } from "../../Stores/ChatStore";
    import { getChatEmojiPicker } from "../../EmojiPicker";
    import { gameManager } from "../../../Phaser/Game/GameManager";
    import { IconArrowBackUp, IconArrowDown, IconMoodSmile, IconPencil, IconTrash } from "@wa-icons";

    export let message: ChatMessage;

    let optionRef: HTMLDivElement;

    function replyToMessage() {
        selectedChatMessageToReply.set(message);
    }

    function removeMessage() {
        message.remove();
    }

    function selectMessageToEdit() {
        selectedChatMessageToEdit.set(message);
    }

    const emojiPicker = getChatEmojiPicker();

    emojiPicker.on("emoji", ({ emoji }) => {
        message.addReaction(emoji).catch((error) => console.error(error));
    });

    function openCloseEmojiPicker() {
        emojiPicker.togglePicker(optionRef);
    }

    const { content, isMyMessage, type } = message;

    const chat = gameManager.getCurrentGameScene().chatConnection;

    $: isGuest = chat.isGuest;
</script>

<div class="tw-flex tw-flex-row tw-gap-1 tw-items-center" bind:this={optionRef}>
    {#if message.type !== "text"}
        <a
            href={$content.url}
            download={$content.body}
            class="tw-p-0 tw-m-0 tw-text-white hover:tw-text-white"
            target="_blank"
        >
            <IconArrowDown font-size={16} class="hover:tw-cursor-pointer hover:tw-text-secondary" />
        </a>
    {/if}
    <button class="tw-p-0 tw-m-0 hover:tw-text-black" data-testid="replyToMessageButton" on:click={replyToMessage}>
        <IconArrowBackUp font-size={16} />
    </button>
    <button
        data-testid="openEmojiPickerButton"
        class="tw-p-0 tw-m-0 hover:tw-text-yellow-500"
        on:click={openCloseEmojiPicker}
    >
        <IconMoodSmile font-size={16} />
    </button>
    {#if isMyMessage && type === "text"}
        <button
            class="tw-p-0 tw-m-0 hover:tw-text-blue-500"
            data-testid="editMessageButton"
            on:click={selectMessageToEdit}
        >
            <IconPencil font-size={16} />
        </button>
    {/if}
    {#if $isGuest === false}
        <button class="tw-p-0 tw-m-0 hover:tw-text-red-500" data-testid="removeMessageButton" on:click={removeMessage}>
            <IconTrash font-size={16} />
        </button>
    {/if}
</div>
