<script>
    import { Motion, useMotionValue, useTransform } from "svelte-motion";
    import InlineSVG from "svelte-inline-svg";
    import SvelteTable from "svelte-table";
    let rows = [
        {
            id: 1,
            event: "Hull Breach",
            description:
                "Everyone at full health loses one [HEART]. `MANDATORY`",
        },
        {
            id: 2,
            event: "Blood Transfusion",
            description:
                "May swap your parasite card with someone not directly next to you.",
        },
        {
            id: 3,
            event: "Stimpac",
            description:
                "Gain one temporary [HEART]. This can temporarily increase your max health by one if you are at full [HEART], but is lost when you are damaged.",
        },
        {
            id: 4,
            event: "Disable Comms",
            description:
                "May choose any player. That player may not speak until the end of their next turn. If this occurs during the endgame phase, they may not speak until the game is over.",
        },
        {
            id: 5,
            event: "Promotion",
            description:
                "May switch your role card (if unused) with another player's unused role card.",
        },
        {
            id: 6,
            event: "Demotion",
            description:
                "May switch another player's role card (if unused) with a random extra role card.",
        },
        {
            id: 7,
            event: "Transfer",
            description:
                "May switch your role card (if unused) with a random extra role card.",
        },
        {
            id: 8,
            event: "Adrenaline Boost",
            description:
                "May use your character ability for free, even if already used.",
        },
        {
            id: 9,
            event: "Thought Control",
            description:
                "May use someone else's character ability if it has not been used yet. That player can still use their ability normally afterwards.",
        },
        {
            id: 10,
            event: "Disarm",
            description:
                "May choose to disarm any player. They cannot use their character ability until the end of their next turn.",
        },
        { id: 11, event: "Search", description: "View any player's [ID]." },
        {
            id: 12,
            event: "Grit",
            description:
                "Draw another die from the bag and roll once. If an [ENGINE], ignore and re-roll.",
        },
        {
            id: 13,
            event: "Quick Hands",
            description:
                "May use your reserve die at this moment (if applicable). If you use it, you do not have to discard it.",
        },
        {
            id: 14,
            event: "No Reservations",
            description:
                "May remove anyone's reserve die and return it to the die bag.",
        },
        {
            id: 15,
            event: "Nope",
            description:
                "May take a [CANCEL] die (if available) and reserve it. If you already have a reserve die, you can replace it with the [CANCEL] die.",
        },
        {
            id: 16,
            event: "Mutiny",
            description:
                "May take over as Captain and take their Captain role card (even if they are dead).",
        },
        {
            id: 17,
            event: "Staff Meeting",
            description:
                "The players on either side of you can see each other' ID card.",
        },
        {
            id: 18,
            event: "Full Disclosure",
            description: "Choose any player to see your ID card.",
        },
        {
            id: 19,
            event: "Hyperspace",
            description:
                "May direct the ship to arrive at Earth only one power-up away from endgame.",
        },
        {
            id: 20,
            event: "Wormhole",
            description:
                "Ship immediately arrives at Earth: endgame immediately begins. `MANDATORY`",
        },
    ];
    // define column configs
    const columns = [
        {
            key: "id",
            title: "ID",
            value: (v) => v.id,
            headerClass: "text-left",
        },
        {
            key: "event",
            title: "EVENT",
            value: (v) => v.event,
            sortable: true,
        },
        {
            key: "description",
            title: "DESCRIPTION",
            value: (v) => v.description,
        },
    ];

    const x = useMotionValue(0);

    function handleUniqueToggle() {
        unique_rolls ^= true;
    }
    function handleReset() {
        rolls_started = false;
    }
    function setEvent() {
        return rows[Math.floor(Math.random() * rows.length)];
    }

    let unique_rolls = true;
    let rolls_started = false;
    let status = "hover";
    let tstatus = "tap";

    let current_event = {};
</script>

<svelte:head>
    <title>Burke time</title>
    <html lang="en" />
</svelte:head>

{#if !rolls_started}
    <button id="unique_rolls" on:click={handleUniqueToggle}>
        {unique_rolls ? "unique events" : "duplicate events"}
    </button>
{/if}

<Motion
    drag="x"
    dragConstraints={{ left: 0, right: 0 }}
    style={{ x }}
    transition={{ duration: 0.2 }}
    onHoverStart={({ scale: 2.2, transition: { duration: 0.1 } },
    (_) => (status = "hover started"))}
    onHoverEnd={(_) => (status = "hover ended")}
    onTapStart={(_) => (tstatus = "tap started")}
    onTap={(_) => (tstatus = "tap succesfull")}
    onTapCancel={(_) => (
        (tstatus = "tap cancelled"),
        (rolls_started = true),
        (current_event = setEvent())
    )}
    let:motion
>
    <div class="box center unselectable" use:motion>
        <Motion>
            <InlineSVG src="images/d20.svg" />
        </Motion>
    </div>
</Motion>
<div>{status} - {tstatus}</div>
<div>{rolls_started}</div>
{#if !rolls_started}
    <SvelteTable {rows} {columns} />
{:else}
    <h1>{current_event.event}</h1>
    <div>id: {current_event.id}</div>
    <h1>{current_event.description}</h1>
    <button id="reset" on:click={handleReset}> RESET </button>
{/if}

<style>
    .box {
        background: black;
        border-radius: 30px;
        width: 8em;
        height: 8em;
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style>
