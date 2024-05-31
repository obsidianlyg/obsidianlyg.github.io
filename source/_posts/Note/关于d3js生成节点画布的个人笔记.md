---
title: 关于d3js生成节点画布的个人笔记
date: 2024-05-30 20:15:12
tags:
---
## 实现功能
1. 根据鼠标位置生成节点
2. 根据节点位置通过鼠标拖拽生成连线
3. 实现自定义线段颜色功能
4. 删除节点以及连线功能
5. 实现单个节点拖动功能
6. 实现整条线路的拖动功能
界面如下：
![](https://raw.githubusercontent.com/obsidianlyg/gallery/master/image/20240531081158.png)
## 主要模块介绍
### 绘制连线
```js
const line = svg.selectAll(".line")
    .data(links, d => `${d.source}-${d.target}`);
line.enter()
    .append("line")
    .attr("class", "line")
    .attr("stroke", d => d.color)
    .attr("id", d => d.objId)
    .merge(line)
    .attr("x1", d => nodes.find(n => n.id === d.source).x)
    .attr("y1", d => nodes.find(n => n.id === d.source).y)
    .attr("x2", d => nodes.find(n => n.id === d.target).x)
    .attr("y2", d => nodes.find(n => n.id === d.target).y)
    });
line.exit().remove();
```
### 绘制节点
设置为四种节点类型，初始节点，杆塔，开关，电表箱
```js
const node = svg.selectAll(".node")
    .data(nodes, d => d.id);
node.enter()
	// 这里可以换成circle，加入属性r，10换成原型节点，移除xlink
    .append("image")
    .attr("class", d => `node ${d.type}`)
    .attr("width", iconSize)
    .attr("height", iconSize)
    .attr("xlink:href", d => {
        if (d.type === 'pole') return './pole.png';
        if (d.type === 'meterBox') return './meterBox.png';
        if (d.type === 'switch') return './switch.png';
        if (d.type === 'init') return './init.png';
    })
    .merge(node)
    .attr("x", d => d.x - iconSize / 2)
    .attr("y", d => d.y - iconSize / 2)
    .call(d3.drag()
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended)
    )
    .on("click", function(event, d) {
        if (isDeletingNode) {
            links = links.filter(link => link.source !== d.id && link.target !== d.id);
            nodes = nodes.filter(node => node !== d);
            update();
            // isDeletingNode = false;
        }else{
            console.log("点击了节点", d.name);
        }
    });
node.exit().remove();
```
### 添加节点
```js
svg.on("click", function(event) {
    if (isAddingNode) {
        const coords = d3.pointer(event);
        const id = getUuid();
        const name = `Node${nodes.length + 1}`;
        const type = nodeType;
        nodes.push({ id, name, type, x: coords[0], y: coords[1] });
        update();
    }
});
```
### 完整实现如下
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Line Loss Diagram with D3.js</title>
    <script src="https://d3js.org/d3.v6.min.js"></script>    
    <style>
        .pole {
            fill: orange;
            stroke: black;
            stroke-width: 1px;
        }
        .meterBox {
            fill: green;
            stroke: black;
            stroke-width: 1px;
        }
        .gateway {
            fill: blue;
            stroke: black;
            stroke-width: 1px;
        }
        .switch {
            fill: gray;
            stroke: black;
            stroke-width: 1px;
        }
        .line {
            stroke-width: 2px;
        }
        .label {
            font-size: 12px;
            text-anchor: middle;
        }
        .button-container {
            margin-bottom: 10px;
        }
        button {
            margin-right: 10px;
        }
        #colorPicker {
            display: none;
            position: absolute;
        }
    </style>
</head>
<body>
    <div class="button-container">
        <button id="addNodeButton" onclick="addNodeButton('pole')">新增节点</button>
        <button id="addNodeButton" onclick="addNodeButton('meterBox')">新增表箱</button>
        <button id="addNodeButton" onclick="addNodeButton('switch')">新增开关</button>
        <button id="addLinkButton">连线</button>
        <button id="deleteNodeButton">删除节点</button>
        <button id="deleteLinkButton">删除连线</button>
        <button onclick="logNodeButton()">打印节点</button>
        <button onclick="selectButton()">选择节点</button>
        <button onclick="selectAllButton()">整体拖动</button>
        <input type="color" id="colorPicker">
        <button
            onclick="updateColor()"
        >
            更新颜色
        </button>
    </div>
    </div>
    <svg width="800" height="600">
        <defs>
            <pattern id="grid" width="20" height="20" patternUnits="userSpaceOnUse">
                <path d="M 20 0 L 0 0 0 20" fill="none" stroke="gray" stroke-width="0.5"/>
            </pattern>
        </defs>
        <rect width="100%" height="100%" fill="url(#grid)" />
    </svg>
    <script src="test.js"></script>
    <script>
        const svg = d3.select("svg");
        
        // let nodes = [];
        // let links = [];
        let links = [];
        let nodes = [{
            "id": "ec61a8f4-73ec-46fe-adb2-4473119a006c",
            "name": "init",
            "type": "init",
            "x": 119.99999237060547,
            "y": 43.71427917480469
        }];
  
        // 虚拟数据

//        nodes = jsonData.nodes;
//      links = jsonData.links;

        let isAddingNode = false;
        let isAddingLink = false;
        let isDeletingNode = false;
        let isDeletingLink = false;
        let sourceNode = null;
        let dragLine = null;
        let nodeType = null;
        let selectedLink   = null;
        let allDrag = false;
        const iconSize = 20;

  

        // 绘制节点和连线
        function update() {
            // 绘制连线
            const line = svg.selectAll(".line")
                .data(links, d => `${d.source}-${d.target}`);
            line.enter()
                .append("line")
                .attr("class", "line")
                .attr("stroke", d => d.color)
                .attr("id", d => d.objId)
                .merge(line)
                .attr("x1", d => nodes.find(n => n.id === d.source).x)
                .attr("y1", d => nodes.find(n => n.id === d.source).y)
                .attr("x2", d => nodes.find(n => n.id === d.target).x)
                .attr("y2", d => nodes.find(n => n.id === d.target).y)
                .on("click", function(event, d) {
                    if (isDeletingLink) {
                        links = links.filter(link => link !== d);
                        update();
                        // isDeletingLink = false;
                    } else{
                        selectedLink = d;
                        const colorPicker = document.getElementById("colorPicker");
                        colorPicker.style.display = "block";
                        colorPicker.style.left = `${event.pageX}px`;
                        colorPicker.style.top = `${event.pageY}px`;
                        colorPicker.value = d.color || "#000000";
                        colorPicker.focus();
                    }
                });

            line.exit().remove();

            // 绘制节点
            const node = svg.selectAll(".node")
                .data(nodes, d => d.id);
            node.enter()
                .append("image")
                .attr("class", d => `node ${d.type}`)
                .attr("width", iconSize)
                .attr("height", iconSize)
                .attr("xlink:href", d => {
                    if (d.type === 'pole') return './pole.png';
                    if (d.type === 'meterBox') return './meterBox.png';
                    if (d.type === 'switch') return './switch.png';
                    if (d.type === 'init') return './init.png';
                })
                .merge(node)
                .attr("x", d => d.x - iconSize / 2)
                .attr("y", d => d.y - iconSize / 2)
                .call(d3.drag()
                    .on("start", dragstarted)
                    .on("drag", dragged)
                    .on("end", dragended)
                )
                .on("click", function(event, d) {
                    if (isDeletingNode) {
                        links = links.filter(link => link.source !== d.id && link.target !== d.id);
                        nodes = nodes.filter(node => node !== d);
                        update();
                        // isDeletingNode = false;
                    }else{
                        console.log("点击了节点", d.name);
                    }
                });
            node.exit().remove();

            // 添加标签
            const label = svg.selectAll(".label")
                .data(nodes, d => d.id);
            label.enter()
                .append("text")
                .attr("class", "label")
                .merge(label)
                .attr("x", d => d.x)
                .attr("y", d => d.y - iconSize/2 - 5)
                // .text(d => d.name);
                // .text(d => d.type);
            label.exit().remove();
        }

  

        // 添加新节点

        svg.on("click", function(event) {
            if (isAddingNode) {
                const coords = d3.pointer(event);
                const id = getUuid();
                const name = `Node${nodes.length + 1}`;
                // const type = nodes.length % 3 === 0 ? 'tower' : (nodes.length % 3 === 1 ? 'station' : 'gateway');
                const type = nodeType;
                nodes.push({ id, name, type, x: coords[0], y: coords[1] });
                update();
                // isAddingNode = false; // Reset the flag
            }
        });
        // 拖拽行为的三个方法
        function dragstarted(event, d) {
            if (isAddingLink) {
                sourceNode = d;
                let id = getUuid();
                sourceNode.objId = id;
                dragLine = svg.append("line")
                    .attr("class", "dragLine")
                    .attr("stroke", "gray")
                    .attr("stroke-width", 2)
                    .attr("x1", d.x)
                    .attr("y1", d.y)
                    .attr("x2", d.x)
                    .attr("y2", d.y)
                    .attr("id", id);
            }
            if (!isAddingNode && !isAddingLink && !isDeletingNode && !isDeletingLink) {
                d3.select(this).raise().classed("active", true);
            }
        }

        function dragged(event, d) {
            if (dragLine) {
                dragLine.attr("x2", event.x)
                        .attr("y2", event.y);
            }
            if (!isAddingNode && !isAddingLink && !isDeletingNode && !isDeletingLink) {
                d.x = event.x;
                d.y = event.y;

                d3.select(this)
                    .attr("x", d.x - iconSize / 2)
                    .attr("y", d.y - iconSize / 2);

                // 更新连线的位置
                svg.selectAll(".line")
                    .attr("x1", l => nodes.find(n => n.id === l.source).x)
                    .attr("y1", l => nodes.find(n => n.id === l.source).y)
                    .attr("x2", l => nodes.find(n => n.id === l.target).x)
                    .attr("y2", l => nodes.find(n => n.id === l.target).y);
            }
            if (allDrag) {
                const dx = event.dx;
                const dy = event.dy;

                // 更新被拖动节点的位置
                d.x += dx;
                d.y += dy;

                d3.select(this)
                    .attr("x", d.x - iconSize / 2)
                    .attr("y", d.y - iconSize / 2);

                // 递归更新与当前节点相连的所有节点和连线的位置
                const visitedNodes = new Set();
                updateConnectedNodes(d, dx, dy, visitedNodes);

                // 更新所有连线的位置
                svg.selectAll(".line")
                    .attr("x1", l => nodes.find(n => n.id === l.source).x)
                    .attr("y1", l => nodes.find(n => n.id === l.source).y)
                    .attr("x2", l => nodes.find(n => n.id === l.target).x)
                    .attr("y2", l => nodes.find(n => n.id === l.target).y);

                // 更新所有节点的位置
                svg.selectAll(".node")
                    .attr("x", n => n.x - iconSize / 2)
                    .attr("y", n => n.y - iconSize / 2);
            }
        }
  
        function dragended(event, d) {
            if (dragLine) {
                dragLine.remove();
                dragLine = null;
                const targetNode = nodes.find(n => Math.hypot(n.x - event.x, n.y - event.y) < 10);
                if (targetNode && targetNode !== sourceNode) {
                    const color = determineLinkColor(sourceNode, targetNode);
                    links.push({objId:sourceNode.id, source: sourceNode.id, target: targetNode.id, color: color });
                    update();
                }
                sourceNode = null;
                // isAddingLink = false; // Reset the flag
            }
            if (!isAddingNode && !isAddingLink && !isDeletingNode && !isDeletingLink) {
                d3.select(this).classed("active", false);
            }
        }
  
        /**
         * 递归更新与当前节点相连的所有节点的位置
         * @param {object} node - 当前节点
         * @param {number} dx - x方向的移动距离
         * @param {number} dy - y方向的移动距离
         * @param {Set} visitedNodes - 已访问的节点集合
         */
        function updateConnectedNodes(node, dx, dy, visitedNodes) {
            visitedNodes.add(node.id);
  
            links.forEach(link => {
                if (link.source === node.id && !visitedNodes.has(link.target)) {
                    const targetNode = nodes.find(n => n.id === link.target);
                    targetNode.x += dx;
                    targetNode.y += dy;
                    updateConnectedNodes(targetNode, dx, dy, visitedNodes);
                } else if (link.target === node.id && !visitedNodes.has(link.source)) {
                    const sourceNode = nodes.find(n => n.id === link.source);
                    sourceNode.x += dx;
                    sourceNode.y += dy;
                    updateConnectedNodes(sourceNode, dx, dy, visitedNodes);
                }
            });
        }
  
        function determineLinkColor(sourceNode, targetNode) {
            if (sourceNode.type === 'pole' && targetNode.type === 'pole') {
                return '#1e90ff';
            } else if (sourceNode.type === 'station' && targetNode.type === 'station') {
                return 'green';
            } else if (sourceNode.type === 'gateway' && targetNode.type === 'gateway') {
                return 'blue';
            } else {
                return '#7f8c8d';
            }
        }
        // 按钮事件处理
        function addNodeButton(type) {
            nodeType = type;
            isAddingNode = true;
            isAddingLink = false;
            isDeletingNode = false;
            isDeletingLink = false;
            allDrag = false;
        };
  
        d3.select("#addLinkButton").on("click", function() {
            isAddingLink = true;
            isAddingNode = false;
            isDeletingNode = false;
            isDeletingLink = false;
            allDrag = false;
        });

        d3.select("#deleteNodeButton").on("click", function() {
            isDeletingNode = true;
            isAddingNode = false;
            isAddingLink = false;
            isDeletingLink = false;
            allDrag = false;
        });
  
        d3.select("#deleteLinkButton").on("click", function() {
            isDeletingLink = true;
            isAddingNode = false;
            isAddingLink = false;
            isDeletingNode = false;
            allDrag = false;
        });
        // 整体拖动
        function selectAllButton() {
            isDeletingLink = false;
            isAddingNode = false;
            isAddingLink = false;
            isDeletingNode = false;
            allDrag = true;
        }
  
       // 打印节点信息
        function logNodeButton() {
            console.log("Node type:", nodeType);
            console.log("Adding node:", isAddingNode);
            console.log("Adding link:", isAddingLink);
            console.log("Deleting node:", isDeletingNode);
            console.log("Deleting link:", isDeletingLink);
            console.log("Nodes:", nodes);
            console.log("Links:", links);
        }
        // 生成id
        function getUuid () {
            if (typeof crypto === 'object') {
              if (typeof crypto.randomUUID === 'function') {
                return crypto.randomUUID();
              }
              if (typeof crypto.getRandomValues === 'function' && typeof Uint8Array === 'function') {
                const callback = (c) => {
                  const num = Number(c);
                  return (num ^ (crypto.getRandomValues(new Uint8Array(1))[0] & (15 >> (num / 4)))).toString(16);
                };
                return ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, callback);
              }
            }
            let timestamp = new Date().getTime();
            let perforNow = (typeof performance !== 'undefined' && performance.now && performance.now() * 1000) || 0;
            return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, (c) => {
              let random = Math.random() * 16;
              if (timestamp > 0) {
                random = (timestamp + random) % 16 | 0;
                timestamp = Math.floor(timestamp / 16);
              } else {
                random = (perforNow + random) % 16 | 0;
                perforNow = Math.floor(perforNow / 16);
              }
              return (c === 'x' ? random : (random & 0x3) | 0x8).toString(16);
            });
        };
        // 选择节点的方法
        function selectButton() {
            isDeletingLink = false;
            isAddingNode = false;
            isAddingLink = false;
            isDeletingNode = false;
            allDrag = false;
        }
        /**
         * 更新指定线段的stroke属性
         * @param {string} sourceId - 连线起点节点的ID
         * @param {string} targetId - 连线终点节点的ID
         * @param {string} color - 新的颜色值
         */
         function updateLinkStroke(sourceId, targetId, color) {
            // 查找指定起点和终点的连线
            const link = links.find(l => l.source === sourceId && l.target === targetId);
            if (link) {
                // 更新连线的颜色属性
                link.color = color;
                // 使用D3.js选择器选择指定的连线，并更新其stroke属性
                d3.selectAll(".line")
                    .filter(d => d.source === sourceId && d.target === targetId)
                    .attr("stroke", color);
            }
        }

         // 颜色选择器事件处理
        function updateColor() {
            const colorPicker = document.getElementById("colorPicker");
            selectedLink.color = colorPicker.value;
            updateLinkStroke(selectedLink.source, selectedLink.target, selectedLink.color);
            colorPicker.style.display = 'none';
        }
        update();
    </script>
</body>
</html>
```
