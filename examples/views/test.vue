<template>
    <div id="layoutContainer" style='width: 100%;height: 800px;'></div>
</template>
 
<script>
    // Cell, Node,
    import {Graph, Color, Dom} from '@antv/x6'
    import dagre from 'dagre'
 
    // 自定义节点
    Graph.registerNode('org-node', {
            width: 260,
            height: 88,
            markup: [
                {
                    tagName: 'rect',
                    attrs: {
                        class: 'card'
                    }
                },
                {
                    tagName: 'image',
                    attrs: {
                        class: 'image'
                    }
                },
                {
                    tagName: 'text',
                    attrs: {
                        class: 'rank'
                    }
                },
                {
                    tagName: 'text',
                    attrs: {
                        class: 'name'
                    }
                },
                //增加刪除
                // {
                //     tagName: 'g',
                //     attrs: {
                //         class: 'btn add'
                //     },
                //     children: [
                //         {
                //             tagName: 'circle',
                //             attrs: {
                //                 class: 'add'
                //             }
                //         },
                //         {
                //             tagName: 'text',
                //             attrs: {
                //                 class: 'add'
                //             }
                //         }
                //     ]
                // },
                // {
                //     tagName: 'g',
                //     attrs: {
                //         class: 'btn del'
                //     },
                //     children: [
                //         {
                //             tagName: 'circle',
                //             attrs: {
                //                 class: 'del'
                //             }
                //         },
                //         {
                //             tagName: 'text',
                //             attrs: {
                //                 class: 'del'
                //             }
                //         }
                //     ]
                // }
            ],
            attrs: {
                '.card': {
                    rx: 10,
                    ry: 10,
                    refWidth: '100%',
                    refHeight: '100%',
                    fill: '#FFF',
                    stroke: '#000',
                    strokeWidth: 0,
                    pointerEvents: 'visiblePainted'
                },
                '.image': {
                    x: 16,
                    y: 16,
                    width: 56,
                    height: 56,
                    opacity: 0.7
                },
                '.rank': {
                    refX: 0.95,
                    refY: 0.5,
                    fontFamily: 'Courier New',
                    fontSize: 13,
                    textAnchor: 'end',
                    textVerticalAnchor: 'middle'
                },
                '.name': {
                    refX: 0.95,
                    refY: 0.7,
                    fontFamily: 'Arial',
                    fontSize: 14,
                    fontWeight: '600',
                    textAnchor: 'end'
                },
                '.btn.add': {
                    refDx: -16,
                    refY: 16,
                    event: 'node:add'
                },
                '.btn.del': {
                    refDx: -44,
                    refY: 16,
                    event: 'node:delete'
                },
                '.btn > circle': {
                    r: 10,
                    fill: 'transparent',
                    stroke: '#333',
                    strokeWidth: 1
                },
                '.btn.add > text': {
                    fontSize: 20,
                    fontWeight: 800,
                    stroke: '#000',
                    x: -5.5,
                    y: 7,
                    fontFamily: 'Times New Roman',
                    text: '+'
                },
                '.btn.del > text': {
                    fontSize: 28,
                    fontWeight: 500,
                    stroke: '#000',
                    x: -4.5,
                    y: 6,
                    fontFamily: 'Times New Roman',
                    text: '-'
                }
            }
        },
        true
    )
 
    // 自定义边
    Graph.registerEdge(
        'org-edge',
        {
            zIndex: -1,
            attrs: {
                line: {
                    stroke: '#585858',
                    strokeWidth: 3,
                    sourceMarker: null,
                    targetMarker: null
                }
            }
        },
        true
    )
 
    const male = 'https://gw.alipayobjects.com/mdn/rms_43231b/afts/img/A*kUy8SrEDp6YAAAAAAAAAAAAAARQnAQ'
    const female = 'https://gw.alipayobjects.com/mdn/rms_43231b/afts/img/A*f6hhT75YjkIAAAAAAAAAAAAAARQnAQ'
    // 布局方向
    const dir = 'LR' // LR RL TB BT
 
    export default {
        name: 'index',
 
        data() {
            return {
                visible: false,
                graph: '',
                datalist: [],
                list: [],
                list2: []
            }
        },
 
        mounted() {
            this.zzManageThreeList();
            // await this.showDrawer();
        },
 
        methods: {
            async zzManageThreeList() {
                console.log(this.$api);
                await this.$api.zzManageThreeList().then(res => {
                    this.datalist = res.data.data;
                    this.list = [];
                    this.readNodes(this.datalist)
                })
            },
            readNodes(nodes = [], biao = '') {
                for (let item of nodes) {
                    this.list.push({
                        name: item.name,
                        sort: item.sort,
                        biao: biao
                    })
                    if (item.children && item.children.length) {
                        this.readNodes(item.children, item.name)
                    }
                }
            },
            showDrawer() {
                this.initGraph()
                this.visible = true
            },
 
            initGraph() {
                // 创建画布
                this.graph = new Graph({
                    container: document.getElementById('layoutContainer'),
                    // width: 100%,
                    // height: 800,
                    grid: true,
                    scroller: true,
                    snapline: true,
                    interacting: false,
                    //縮放
                    mousewheel: {
                        enabled: true,
                        modifiers: ['ctrl', 'meta'],
                    },
                });
                var nodes = [
                    // this.createNode('Founder & Chairman', 'Pierre Omidyar', male, '#31d0c6'),
                    // this.createNode('President & CEO', 'Margaret C. Whitman', female, '#31d0c6'),
                    // this.createNode('President, PayPal', 'Scott Thompson', male, '#7c68fc'),
                    // this.createNode('President, Ebay Global Marketplaces', 'Devin Wenig', male, '#7c68fc'),
                    // this.createNode('Senior Vice President Human Resources', 'Jeffrey S. Skoll', male, '#fe854f'),
                    // this.createNode('Senior Vice President Controller', 'Steven P. Westly', male, '#feb663')
                ]
                // nodes.push(this.createNode(this.datalist[0].sort, this.datalist[0].name, male, '#31d0c6'))
                for (let i = 0; i < this.list.length; i++) {
                    nodes.push(this.createNode(this.list[i].sort, this.list[i].name, male, '#31d0c6',this.list[i].biao))
                }
                this.list2=[...this.list]
                const edges = [
                    // this.createEdge(nodes[0], nodes[1]),
                    // this.createEdge(nodes[0], nodes[3]),
                    // this.createEdge(nodes[1], nodes[2]),
                    // // this.createEdge(nodes[1], nodes[3]),
                    // this.createEdge(nodes[1], nodes[4]),
                    // this.createEdge(nodes[1], nodes[5])
                ]
                for (let i=0;i<this.list.length;i++){
                    for (let j=1;j<this.list2.length;j++) {
                        if (this.list[i].name==this.list2[j].biao) {
                            edges.push(this.createEdge(nodes[i], nodes[j]))
                        }
                    }
                }
 
                this.graph.resetCells([...nodes, ...edges])
                this.layout()
                this.graph.zoomTo(0.8)
                this.graph.centerContent()
                // this.setup()
            },
 
            // 监听自定义事件
            setup() {
                this.graph.on('node:add', ({e, node}) => {
                    e.stopPropagation()
                    const bg = Color.randomHex()
                    const member = this.createNode(
                        'Employee',
                        'New Employee',
                        Math.random() < 0.5 ? male : female,
                        bg,
                        Color.invert(bg, true)
                    )
                    this.graph.freeze()
                    this.graph.addCell([member, this.createEdge(node, member)])
                    this.layout()
                })
 
                this.graph.on('node:delete', ({e, node}) => {
                    e.stopPropagation()
                    this.graph.freeze()
                    this.graph.removeCell(node)
                    this.layout()
                })
            },
            // 自动布局
            layout() {
                const nodes = this.graph.getNodes()
                const edges = this.graph.getEdges()
                const g = new dagre.graphlib.Graph()
                g.setGraph({rankdir: dir, nodesep: 16, ranksep: 16})
                g.setDefaultEdgeLabel(() => ({}))
 
                const width = 260
                const height = 90
                nodes.forEach((node) => {
                    g.setNode(node.id, {width, height})
                })
 
                edges.forEach((edge) => {
                    const source = edge.getSource()
                    const target = edge.getTarget()
                    g.setEdge(source.cell, target.cell)
                })
 
                dagre.layout(g)
 
                this.graph.freeze()
 
                g.nodes().forEach((id) => {
                    const node = this.graph.getCell(id)
                    if (node) {
                        const pos = g.node(id)
                        node.position(pos.x, pos.y)
                    }
                })
 
                edges.forEach((edge) => {
                    const source = edge.getSourceNode()
                    const target = edge.getTargetNode()
                    const sourceBBox = source.getBBox()
                    const targetBBox = target.getBBox()
 
                    // console.log(sourceBBox, targetBBox)
 
                    if ((dir === 'LR' || dir === 'RL') && sourceBBox.y !== targetBBox.y) {
                        const gap =
                            dir === 'LR'
                                ? targetBBox.x - sourceBBox.x - sourceBBox.width
                                : -sourceBBox.x + targetBBox.x + targetBBox.width
                        const fix = dir === 'LR' ? sourceBBox.width : 0
                        const x = sourceBBox.x + fix + gap / 2
                        edge.setVertices([
                            {x, y: sourceBBox.center.y},
                            {x, y: targetBBox.center.y}
                        ])
                    } else if (
                        (dir === 'TB' || dir === 'BT') &&
                        sourceBBox.x !== targetBBox.x
                    ) {
                        const gap =
                            dir === 'TB'
                                ? targetBBox.y - sourceBBox.y - sourceBBox.height
                                : -sourceBBox.y + targetBBox.y + targetBBox.height
                        const fix = dir === 'TB' ? sourceBBox.height : 0
                        const y = sourceBBox.y + fix + gap / 2
                        edge.setVertices([
                            {x: sourceBBox.center.x, y},
                            {x: targetBBox.center.x, y}
                        ])
                    } else {
                        edge.setVertices([])
                    }
                })
 
                this.graph.unfreeze()
            },
 
            createNode(rank, name, image, background) {
                const textColor = '#000';
                return this.graph.createNode({
                    shape: 'org-node',
                    attrs: {
                        '.card': {fill: background},
                        '.image': {xlinkHref: image},
                        '.rank': {
                            fill: textColor,
                            text: Dom.breakText(rank, {width: 160, height: 45})
                        },
                        '.name': {
                            fill: textColor,
                            text: Dom.breakText(name, {width: 160, height: 45})
                        },
                        '.btn > circle': {stroke: textColor},
                        '.btn > text': {fill: textColor, stroke: textColor}
                    }
                })
            },
            createEdge(source, target) {
                return this.graph.createEdge({
                    shape: 'org-edge',
                    source: {cell: source.id},
                    target: {cell: target.id}
                })
            }
        }
    }
</script>
 
<style scoped>
 
</style>