<script>
    import InlineSVG from "svelte-inline-svg";
    import { Motion } from "svelte-motion";

    let unique_rolls = true;
    let rolls_started = false;

    function handleClick() {
        unique_rolls ^= true;
    }
</script>

<button id="unique_rolls" on:click={handleClick}>
    Is {unique_rolls ? "unique" : "not unique"}
</button>
<Motion let:motion><div use:motion /></Motion>

