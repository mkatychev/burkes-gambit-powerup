<script>
import { onMount } from "svelte";
import Selecto from "svelte-selecto";
import Moveable from "svelte-moveable";

const frameMap = new Map();
const cubes = [];
let targets = [];
let moveable;
let selecto;

for (let i = 0; i < 30; ++i) {
    cubes.push(i);
}
</script>
<style>
    html, body, #root {
        position: relative;
        margin: 0;
        padding: 0;
        height: 100%;
        color: #333;
        background: #fdfdfd;
    }
    
    .app {
        position: relative;
        min-height: 100%;
        padding: 10px 20px;
        text-align: center;
        display: flex;
        align-items: center;
        justify-content: center;
        box-sizing: border-box;
    }
    
    .container {
        max-width: 800px;
        ;
    }
    
    body {
        background: #fff;
    }
    
    .logo {
        position: relative;
        width: 150px;
        height: 150px;
        margin: 0px auto;
        font-size: 0;
        text-align: left;
    }
    
    .logo.logos {
        width: 320px;
        text-align: center;
    }
    
    .logos .selecto {
        padding: 16px;
    }
    
    .logo img {
        position: relative;
        height: 100%;
        box-sizing: border-box;
    }
    
    .cube {
        display: inline-block;
        border-radius: 5px;
        width: 40px;
        height: 40px;
        margin: 4px;
        background: #eee;
        --color: #4af;
    }
    
    h1, .description {
        text-align: center;
    }
    
    .button {
        border: 1px solid #333;
        color: #333;
        background: transparent;
        appearance: none;
        -webkit-appearance: none;
        box-sizing: border-box;
        cursor: pointer;
        width: 120px;
        height: 42px;
        font-size: 14px;
        letter-spacing: 1px;
        transition: all ease 0.2s;
        margin: 0px 5px;
    }
    
    .button:hover {
        background: #333;
        color: white;
    }
    
    .elements {
        margin-top: 40px;
        border: 2px solid #eee;
    }
    
    .selecto-area {
        padding: 20px;
    }
    
    #selecto1 .cube {
        transition: all ease 0.2s;
    }
    
    .moveable #selecto1 .cube {
        transition: none;
    }
    
    .selecto-area :global(.selected) {
        color: #fff;
        background: var(--color);
    }
    
    .scroll {
        overflow: auto;
        padding-top: 10px;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }
    
    :global(.infinite-viewer), .scroll {
        width: 100%;
        height: 300px;
        box-sizing: border-box;
    }
    
    :global(.infinite-viewer) .viewport {
        padding-top: 10px;
    }
    
    .empty.elements {
        border: none;
    }
</style>

<div class="moveable app">
    <div class="container">
        <p class="description">You can drag and move targets and select them.</p>
        <Moveable
            bind:this={moveable}
            draggable={true}
            target={targets}
            on:clickGroup={({ detail: e }) => {
                selecto.clickTarget(e.inputEvent, e.inputTarget);
            }}
            on:dragStart={({ detail: e }) => {
                const target = e.target;
                console.log("moveableDragStart");
            
                if (!frameMap.has(target)) {
                    frameMap.set(target, {
                        translate: [0, 0],
                    });
                }
                const frame = frameMap.get(target);
           
                e.set(frame.translate);
            }}
            on:drag={({ detail: e }) => {
                console.log("drag");
                const target = e.target;
                const frame = frameMap.get(target);
            
                frame.translate = e.beforeTranslate;
                target.style.transform = `translate(${frame.translate[0]}px, ${frame.translate[1]}px)`;
            }}
            on:dragGroupStart={({ detail: e }) => {
                e.events.forEach(ev => {
                    const target = ev.target;
            
                    if (!frameMap.has(target)) {
                        frameMap.set(target, {
                            translate: [0, 0],
                        });
                    }
                    const frame = frameMap.get(target);
            
                    ev.set(frame.translate);
                });
            }}
            on:dragGroup={({ detail: e }) => {
                e.events.forEach(ev => {
                    const target = ev.target;
                    const frame = frameMap.get(target);
            
                    frame.translate = ev.beforeTranslate;
                    target.style.transform = `translate(${frame.translate[0]}px, ${frame.translate[1]}px)`;
                });
            }}
        ></Moveable>
        <Selecto
            bind:this={selecto}
            dragContainer={".elements"}
            selectableTargets={[".selecto-area .cube"]}
            hitRate={0}
            selectByClick={true}
            selectFromInside={false}
            toggleContinueSelect={["shift"]}
            ratio={0}
            on:dragStart={({ detail: e }) => {
                console.log("selectoDragStart");
                const target = e.inputEvent.target;
                if (
                    moveable.isMoveableElement(target)
                    || targets.some(t => t === target || t.contains(target))
                ) {
                    e.stop();
                }
            }}
            on:select={({ detail: e }) => {
                targets = e.selected;
                /* moveable.target = targets; */
            }}
            on:selectEnd={({ detail: e }) => {
                if (e.isDragStart) {

                    e.inputEvent.preventDefault();
            
                    setTimeout(() => {
                         moveable.dragStart(e.inputEvent);
                    });
                }
            }}
        ></Selecto>

        <div class="elements selecto-area">
            {#each cubes as cube}
                <div class="cube"></div>
            {/each}
        </div>
        <div class="empty elements"></div>
    </div>
</div>
