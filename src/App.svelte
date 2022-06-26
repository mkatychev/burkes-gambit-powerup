<script>
    import InlineSVG from 'svelte-inline-svg'

    const d20 = "../images/d20.svg"

    let unique_rolls = true;
    let rolls_started = false;

    function handleClick() {	
        unique_rolls ^= true;
    }
</script>

<button id="unique_rolls" on:click={handleClick}>
	Is {unique_rolls ? 'unique' : 'not unique'}
</button>							

<InlineSVG src={d20} />
