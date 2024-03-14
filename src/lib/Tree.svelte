<script lang="ts">
	import Katex from "./Latex.svelte"
    
    type Point = {
        x: number,
        y: number,
    };
    type Line = {
        p1: Point;
        p2: Point;
        height: number;
    };

    type Node = {
        id: number,
        dist: number,
        height: number,
        rowId: number,
        point: Point | null
        parentPoint: Point | null
    };

    // tree parameters
    let n : number = 27;
    let c : number = 3;
    let m : number = 0.5;
    var stochasticLevel = 1; 
    const SCALE_FACTOR = 1; 
    // branch structures
    let lines : Line[] = [];
    let dfsNodeArr : Node[] = [];

    const transform = (attatchPoint: Point, node: Node) => {
        var theta: number;
        var thetaUnit: number = Math.PI /(c+1);
        if (node.height == 0) {
            theta = Math.PI / 2;
        } else {
            theta = thetaUnit * (node.rowId % c + 1);
            if (stochasticLevel > 1) {
                theta += (Math.random() -0.5) * 1 / stochasticLevel;
            }
        } 

        let delta_x = node.dist * Math.cos(theta) * SCALE_FACTOR;
        let delta_y = node.dist * Math.sin(theta) * SCALE_FACTOR; 
        if (stochasticLevel > 1) {
            delta_x += (Math.random() - 0.5) * stochasticLevel;
            delta_y += (Math.random() - 0.5) * stochasticLevel;
        }
        let newPoint: Point = {
            x: attatchPoint.x + (delta_x), 
            y: attatchPoint.y - (delta_y), 
        };
        node.parentPoint = attatchPoint;
        node.point = newPoint;
    }
    
    const initializeNodeCoords = (nodeMap: Node[]) => {

        const root : Point = {x: 300, y: 350};
        let newNodes: Node[] = []; 
        let stack: Node[] = [];
        let rootNode: Node = {
            id: 0, dist: -1, 
            height: -1, rowId: 0, 
            point: root, parentPoint: null
        };

        let attatchNode: Node = rootNode;
        let prevNode: Node = rootNode;
        stack.push(rootNode); // push root
        nodeMap.map(node => {
        
            // manage stack 
            if (node.height > prevNode.height) {
                stack.push(prevNode);
            } else if (node.height < prevNode.height) {
                let diff = prevNode.height - node.height;
                for (i = 0; i < diff; i++) {
                    stack.pop();
                }
            }
            // assign attatch node
            attatchNode = stack[stack.length -1]; 
            if (attatchNode.point) {
                transform(attatchNode.point, node);
                newNodes.push(node);
            }
            prevNode = node;    
        });
        newNodes.map(node => {
            if (node.parentPoint && node.point) {
                let line: Line = {p1: node.parentPoint, p2: node.point, height: node.height};
                lines.push(line);
            }
        })
    }

    let i = 0;
    let rowMap: { [key: string]: number } = {};

    const pushRecursiveNode = (n: number, h: number) => {
        const heightKey = h.toString();
        if (rowMap[heightKey]) {
            rowMap[heightKey]++;
        } else {
            rowMap[heightKey] = 1;
        } 
        
        let node: Node = {
            id: i+1, dist: n, 
            height: h, rowId: rowMap[heightKey],
            point: null, parentPoint: null 
        }
         
        dfsNodeArr.push(node);
        i++;
    }


    interface GrowTreeCall {
        n: number;
        h: number;
    }

    const growTree = (length: number, height: number = 0) => {
        let stack: GrowTreeCall[] = [{ n: length, h: height }];

        while (stack.length > 0) {
            let { n, h } = stack.pop();

            // Base case
            if (n <= 1) {
                continue;
            }

            pushRecursiveNode(n, h);

            // Assuming c and m are defined and accessible in your scope
            for (let i = 0; i < c; i++) {
                stack.push({ n: n * m, h: h + 1 });
            }
        }
    };

    const colorTreeBranch = (height: number) => {

        const darkBrown = { r: 101, g: 67, b: 33 };
        const green = { r: 0, g: 128, b: 0 };
        const fallColors = [
            { r: 0, g: 173, b: 43 },
            { r: 0, g: 128, b: 32},
            { r: 77, g: 171, b: 14 },
            { r: 131, g: 158, b: 9},
        ];

        const normalizedHeight = Math.min(Math.max(height / 10, 0), 1);

        let color;
        if (Math.random() < normalizedHeight * 0.5) {
            color = fallColors[Math.floor(Math.random() * fallColors.length)];
        } else {
            // linear interpolation between dark brown and green
            const r = Math.round(darkBrown.r + (green.r - darkBrown.r) * normalizedHeight);
            const g = Math.round(darkBrown.g + (green.g - darkBrown.g) * normalizedHeight);
            const b = Math.round(darkBrown.b + (green.b - darkBrown.b) * normalizedHeight);
            color = { r, g, b };
        }
        return `rgb(${color.r},${color.g},${color.b})`;
    }

    $: {
        if (n || c || m || stochasticLevel) {
            i = 0;
            dfsNodeArr = [];
            lines=[];
            growTree(n);
            initializeNodeCoords(dfsNodeArr);
        }
    }
 
    let zoomLevel = 6;
    let viewBox = {
        x: 0,
        y: 0,
        width: 600,
        height: 600
    };

    let isDragging = false;
    let startX: number, startY: number;

    function startDrag(event: MouseEvent) {
        isDragging = true;
        startX = event.clientX;
        startY = event.clientY;
    }

    function handleZoom(event: WheelEvent) {
        event.preventDefault();
        const zoomSensitivity = 0.5;
        if (event.deltaY < 0) {
            zoomLevel = Math.min(zoomLevel + zoomSensitivity, 15); // Max zoom level
        } else {
            zoomLevel = Math.max(zoomLevel - zoomSensitivity, 0.01); // Min zoom level
        }
    }
    function onDrag(event: MouseEvent) {
        if (isDragging) {
            const dx = (event.clientX - startX) * 1/zoomLevel;
            const dy = (event.clientY - startY) * 1/zoomLevel;
            viewBox.x -= dx;
            viewBox.y -= dy;
            startX = event.clientX;
            startY = event.clientY;
        }
    }

    function endDrag() {
        isDragging = false;
    }

    $: {
        let midX = viewBox.width / 2 + viewBox.x;
        let midY = viewBox.height / 2 + viewBox.y;
        viewBox.width = 600 / zoomLevel;
        viewBox.height = 600 / zoomLevel;
        viewBox.x = midX - viewBox.width / 2;
        viewBox.y = midY - viewBox.height / 2;
    }
    
    $: latexString = `T(n) = ${c}T(${m}n)`;
    $: nString = `n=${n}`;
    $: coefficientLatexString = `c: ${c}`;
    $: fractionLatexString = `m: ${m}`;
    $: nLatexString = `T(${n})`;

</script>
<div class="project-container">
    <div class="project-latex">
        <Katex math={latexString}/>
    </div>
    <div class="n-latex">
        <Katex math={nString}/>
    </div>
    <div class="project-controls">
        <Katex math={coefficientLatexString}></Katex><input type="range" min="1" max="5" bind:value={c} />
        <Katex math={fractionLatexString}></Katex><input type="range" min="0.125" max="0.5" step="0.125" bind:value={m} />
        <Katex math={nLatexString}></Katex><input type="range" min="2" max="256" bind:value={n} />
        <div>
            Zoom: <input type="range" min="0.01" max="10" step="0.1" bind:value={zoomLevel} />
            Stochasticity: <input type="range" min="1" max="10" step="0.1" bind:value={stochasticLevel} />
        </div> 
    </div> 
     
    <!-- svelte-ignore a11y-no-static-element-interactions -->
    <div class="tree-container"
        on:mousedown={startDrag}
         on:mousemove={onDrag}
         on:mouseup={endDrag}
         on:mouseleave={endDrag}
         on:wheel={handleZoom}
    >
        <svg id="tree-svg" width="800" height="650" viewBox={`${viewBox.x} ${viewBox.y} ${viewBox.width} ${viewBox.height}`}>
            {#each lines as line, i}
                <line 
                    x1={line.p1.x} y1={line.p1.y} 
                    x2={line.p2.x} y2={line.p2.y}
                    stroke={colorTreeBranch(line.height)} />
            {/each}
        </svg>
    </div> 
</div>
<style>
    #tree-svg:hover {
        cursor: pointer;
    }
    .tree-container {
        margin: auto;
        width: min-content;
        height: min-content;
        transform-origin: center;
        margin-bottom: 40px;
    }
    .project-container {
        display: flex;
        flex-grow: 1;
        justify-content: center;
        align-items: center;
        flex-direction: column;
    }
    .project-latex {
        margin-top: 30px;
        font-size: 28px;
    }
    .n-latex {
        margin-top: 10px;
        font-size: 24px;
    }
    .project-controls {
        margin: auto;
        margin-top: 40px;
        text-align: center;
        font-size: 18px;
    } 
</style>