<script lang="ts">
    import { createEventDispatcher } from "svelte";
    import { gameManager } from "../../Phaser/Game/GameManager";
    import InputTags from "./InputTags.svelte";
    import { InputTagOption } from "./InputTagOption";

    const dispatch = createEventDispatcher();

    export let value: InputTagOption[] | undefined;

    function _handleChange() {
        dispatch("change", value);
    }

    async function searchRoomTags(filterText: string): Promise<{ value: string; label: string }[]> {
        const customTag = {
            value: filterText,
            label: `add a new tag: '${filterText}'`,
        };
        if (filterText.length === 0) return [];
        const connection = gameManager.getCurrentGameScene().connection;
        if (connection) {
            try {
                const tags = await connection.queryTags(filterText);
                const result = tags.map((tag) => {
                    return {
                        value: tag,
                        label: tag,
                    };
                });
                result.push(customTag);
                return result;
            } catch (error) {
                console.error(error);
                return [customTag];
            }
        }
        return [customTag];
    }
</script>

<div>
    <InputTags bind:value queryOptions={searchRoomTags} on:change={_handleChange} {...$$props} />
</div>
