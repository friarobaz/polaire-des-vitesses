<script>
    import { onMount } from 'svelte';
    import {drawMouseLayer, drawCurveLayer} from './draw'
    import {drawGrid} from './grid'
    let gridCanvas, curveCanvas, mouseCanvas, grid, curve, mouse, container
    const CANVAS_HEIGHT = window.innerHeight - 300
    const CANVAS_WIDTH = window.innerWidth - 700
    
    let settings = {
        width: CANVAS_WIDTH,
        height: CANVAS_HEIGHT,
    }
    const defaultCurveNumbers = {
        A:58,
        B:3,
        C:85,
        D:500,
        E:90
    }
    let mouseCoordinates = {x:0, y:0}
    let origin = {x:0, y:0}
    let curveNumbers = structuredClone(defaultCurveNumbers)
    let showEntireCurve = false
    let showCurveControls = false
    let showAllPTV = false
    let showBestGlide  =false
    let showGlide  =false
    let showCursorHelper = true

    onMount(() => {
        gridCanvas.width = CANVAS_WIDTH;
        curveCanvas.width = CANVAS_WIDTH;
        mouseCanvas.width = CANVAS_WIDTH;
        gridCanvas.height = CANVAS_HEIGHT;
        curveCanvas.height = CANVAS_HEIGHT;
        mouseCanvas.height = CANVAS_HEIGHT;
        grid = gridCanvas.getContext("2d")
        curve = curveCanvas.getContext("2d")
        mouse = mouseCanvas.getContext("2d")
        drawGrid(grid, CANVAS_HEIGHT, CANVAS_WIDTH, 10, "rgba(0, 100, 255, 0.05)")
        drawGrid(grid, CANVAS_HEIGHT, CANVAS_WIDTH, 50, "rgba(255, 150, 0, 0.15)")
        drawCurveLayer(curve, settings)
    })

    document.onmousemove = e =>{
        const x = e.clientX - container.offsetLeft
        const y = e.clientY - container.offsetTop
        mouseCoordinates.x = x
        mouseCoordinates.y = y
        mouse.clearRect(0, 0, settings.width, settings.height)
        if (x >= 0 && x <= CANVAS_WIDTH && y >= 0 && y <= CANVAS_HEIGHT) {
            console.log('updating mouse')
            drawMouseLayer(mouse, mouseCoordinates, origin, curveNumbers, showGlide, showCursorHelper)
        }
    }

    $:if (curve) {
        console.log('updating curve')
        curve.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
        drawCurveLayer(curve, curveNumbers, origin, showEntireCurve, showAllPTV, showBestGlide)
    }

    const reset = ()=>{
        origin = {x:0, y:0};
        curveNumbers = structuredClone(defaultCurveNumbers)
    }

</script>

<div id="container" bind:this={container}>
 <canvas id="grid" bind:this={gridCanvas}></canvas>
 <canvas id="curve" bind:this={curveCanvas}></canvas>
 <canvas id="mouse" bind:this={mouseCanvas}></canvas>
</div>

<br>
<div id="controls">

<div>
        Vent de face:<input type="range" min=-300 max=200 bind:value={origin.x} > {origin.x} <br>
        Thermique:<input type="range" min=-100 max=100 bind:value={origin.y} > {origin.y}
    </div>
    <br>
    <button on:click={reset}>Reinitialiser</button>
    <br>
      Montrer différentes charges alaires : 
    <input type="checkbox" bind:checked={showAllPTV}>
    <br>
    Montrer la finesse max : 
  <input type="checkbox" bind:checked={showBestGlide}>
  <br>
    Montrer la finesse à la souris : 
  <input type="checkbox" bind:checked={showGlide}>
  <br>
  Montrer les valeurs : 
<input type="checkbox" bind:checked={showCursorHelper}>
<br>
    Modifier la polaire : 
<input type="checkbox" bind:checked={showCurveControls}>
<br>
    {#if showCurveControls}
    
        
            A: <input type="range" min=0 max=100 bind:value={curveNumbers.A}>
            B: <input type="range" min=1 max=10 step=0.1 bind:value={curveNumbers.B} >
            C: <input type="range" min=40 max=120 bind:value={curveNumbers.C} >
            D:<input type="range" min=0 max=1000 bind:value={curveNumbers.D} >
            E:<input type="range" min=0 max=200 bind:value={curveNumbers.E} >
            <br>
            A: {curveNumbers.A}
            B: {curveNumbers.B}
            C: {curveNumbers.C}
            D: {curveNumbers.D}
            E: {curveNumbers.E}
            <br>
            
            <div>
                Montrer toute la courbe : 
                <input type="checkbox" bind:checked={showEntireCurve}>
            </div>
            {/if}
        </div>






<style>
    #container{
        position: relative;
    }
    #controls{
        background-color: burlywood;
        position: absolute;
        bottom: 0;
        padding: 20px;
        z-index: 10;
        width: 100%;
    }
    
    canvas{
        cursor: crosshair;
        position: absolute;
        left: 0;
        top: 0;
    }

    #grid{
        z-index: 0;
    }
    #curve{
        z-index: 1;
    }
    #mouse{
        z-index: 2;
    }
</style>