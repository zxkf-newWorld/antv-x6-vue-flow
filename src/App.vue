<template>
	<div
		id="coverCot"
		style="width: 100vw; height: 100vh; overflow: hidden"
	>
		<PageHeader />
		<section
			class="section-cot"
			style="width: 100%; height: 100%"
		>
			<div id="container" @click.stop="hideFn">
				<MenuBar
					v-if="showContextMenu"
					ref="menuBar"
					@callBack="contextMenuFn"
				/>
				<header v-show="false">
					<el-tooltip
						class="item"
						effect="dark"
						content="项目"
						placement="bottom"
					>
						<i
							class="el-icon-menu"
							@click="showDrawerFn()"
						/>
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="长按shift多选"
						placement="bottom"
					>
						<i class="el-icon-crop" />
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="放大"
						placement="bottom"
					>
						<i
							class="el-icon-zoom-in"
							@click="zoomFn(0.2)"
						/>
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="缩小"
						placement="bottom"
					>
						<i
							class="el-icon-zoom-out"
							@click="zoomFn(-0.2)"
						/>
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="适应屏幕"
						placement="bottom"
					>
						<i
							class="el-icon-full-screen"
							@click="centerFn"
						/>
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="执行"
						placement="bottom"
					>
						<i
							class="el-icon-video-play"
							@click="startFn()"
						/>
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="保存"
						placement="bottom"
					>
						<i class="el-icon-upload" @click="saveFn()" />
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="加载保存页面"
						placement="bottom"
					>
						<i class="el-icon-link" @click="loadFn()" />
					</el-tooltip>
					<el-tooltip
						class="item"
						effect="dark"
						content="是否禁用"
						placement="bottom"
					>
						<i
							:class="{
								'el-icon-lock': isLock,
								'el-icon-unlock': !isLock,
							}"
							@click="lockFn()"
						/>
					</el-tooltip>
				</header>
				<div id="draw-cot" />
				<Drawer ref="drawer" @addNode="addNode" />
				<right-drawer ref="rightDrawer"></right-drawer>
			</div>
		</section>

		<DialogCondition
			ref="dialogCondition"
		></DialogCondition>
		<DialogMysql ref="dialogMysql"></DialogMysql>
	</div>
</template>

<script>
	import { Graph, Node, Path, Cell } from '@antv/x6'
	import '@antv/x6-vue-shape'

	import PageHeader from './components/header.vue'
	import database from './components/nodeTheme/database.vue'
	import condition from './components/nodeTheme/condition.vue'
	import onlyout from './components/nodeTheme/onlyOut.vue'
	import onlyin from './components/nodeTheme/onlyIn.vue'
	import DataJson from './data'
	import MenuBar from './components/menuBar'
	import Drawer from './components/drawer'
	import RightDrawer from './components/rightDrawer.vue'
	import DialogCondition from './components/dialog/condition.vue'
	import DialogMysql from './components/dialog/mysql.vue'
	import { rect } from '@antv/x6/lib/registry/connection-point/rect'
	import { right } from '@antv/x6/lib/registry/port-layout/line'

	const nodeStatusList = [
		[
			{
				id: '1',
				status: 'running',
			},
			{
				id: '2',
				status: 'default',
			},
			{
				id: '3',
				status: 'default',
			},
			{
				id: '4',
				status: 'default',
			},
		],
		[
			{
				id: '1',
				status: 'success',
			},
			{
				id: '2',
				status: 'running',
			},
			{
				id: '3',
				status: 'default',
			},
			{
				id: '4',
				status: 'default',
			},
		],
		[
			{
				id: '1',
				status: 'success',
			},
			{
				id: '2',
				status: 'success',
			},
			{
				id: '3',
				status: 'running',
			},
			{
				id: '4',
				status: 'running',
			},
		],
		[
			{
				id: '1',
				status: 'success',
			},
			{
				id: '2',
				status: 'success',
			},
			{
				id: '3',
				status: 'success',
			},
			{
				id: '4',
				status: 'failed',
			},
		],
	]

	export default {
		name: 'App',
		components: {
			MenuBar,
			PageHeader,
			Drawer,
			RightDrawer,
			DialogCondition,
			DialogMysql,
		},
		data() {
			return {
				graph: '',
				timer: '',
				isLock: false,
				showContextMenu: false,
			}
		},
		mounted() {
			// 初始化 graph
			this.initGraph()
			// 按钮绑定
			this.keyBindFn()
			// 执行
			// this.startFn()
			// 测试部分
			this.test()
		},
		methods: {
			test() {
				// 通过graph添加节点/边
				this.testAddCell()
			},
			testAddCell() {
				this.graph.addNode({
					shape: 'rect',
					x: 350,
					y: 200,
					width: 100,
					height: 100,
					label: '矩形',
					ports: [
						{ id: 'port1' },
						{
							id: 'port2',
							attrs: {
								circle: {
									magnet: true, // 可吸附
									r: 6,
									stroke: '#ffaa00',
									fill: 'transparent',
									strokeWidth: 1,
								},
							},
						},
					],
				})
				this.graph.addEdge({
					source: { x: 540, y: 40 },
					target: { x: 580, y: 180 },
					vertices: [
						{
							x: 580,
							y: 180,
						},
						{ x: 540, y: 140 },
						{
							x: 590,
							y: 190,
						},
						{ x: 550, y: 150 },
					],
					connector: { name: 'smooth' },
					attrs: {
						line: {
							stroke: '#1890ff',
							strokeDasharray: 5,
							targetMarker: 'classic',
							style: {
								animation: 'ant-line 30s infinite linear',
							},
						},
					},
				})

				this.graph.addEdge({
					source: { x: 320, y: 100 },
					target: { x: 320, y: 260 },
					// vertices: [{ x: 320, y: 200 }],
					shape: 'arrow',
					// connector: { name: 'rounded' },
					attrs: {
						line: {
							stroke: '#73d13d',
							strokeWidth: 3,
						},
						arrow: {
							// d: 'M 0 -6 8 0 0 6 z',
							// fill: '#ED8A19',
							fill: 'transparent',
							stroke: '#ED8A19',
						},
					},
					tools: [
						{
							name: 'button',
							args: {
								markup: [
									{
										tagName: 'circle',
										selector: 'button',
										attrs: {
											r: 10,
											stroke: '#fe854f',
											strokeWidth: 2,
											fill: 'red',
											cursor: 'pointer',
										},
									},
									// {
									// 	tagName: 'text',
									// 	textContent: 'Btn B',
									// 	selector: 'icon',
									// 	attrs: {
									// 		fill: '#fe854f',
									// 		fontSize: 10,
									// 		textAnchor: 'middle',
									// 		pointerEvents: 'none',
									// 		y: '0.3em',
									// 	},
									// },
								],
								distance: '50%',
								onClick({ view }) {
									const edge = view.cell
									const source = edge.getSource()
									const target = edge.getTarget()
									edge.setSource(target)
									edge.setTarget(source)
								},
							},
						},
					],
				})
			},
			handleRightDrawerClose() {
				alert('关闭抽屉')
			},
			getNodeById(id) {
				return this.graph.getCellById(id)
			},
			hideFn() {
				this.showContextMenu = false
			},
			initGraph() {
				const vm = this
				// 注册节点
				Graph.registerNode(
					'dag-condition',
					{
						inherit: 'vue-shape',
						width: 180,
						height: 36,
						component: {
							template: `<condition />`,
							components: {
								condition,
							},
						},
						ports: {
							groups: {
								top: {
									position: 'top',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
								bottom: {
									position: 'bottom',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
							},
						},
					},
					true
				)

				Graph.registerNode(
					'dag-output',
					{
						inherit: 'vue-shape',
						width: 180,
						height: 36,
						component: {
							template: `<onlyout />`,
							components: {
								onlyout,
							},
						},
						ports: {
							groups: {
								top: {
									position: 'top',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
								bottom: {
									position: 'bottom',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
							},
						},
					},
					true
				)

				Graph.registerNode(
					'dag-onlyIn',
					{
						inherit: 'vue-shape',
						width: 180,
						height: 36,
						component: {
							template: `<onlyin />`,
							components: {
								onlyin,
							},
						},
						ports: {
							groups: {
								top: {
									position: 'top',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
								bottom: {
									position: 'bottom',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
							},
						},
					},
					true
				)

				Graph.registerNode(
					'dag-node',
					{
						inherit: 'vue-shape',
						width: 180,
						height: 36,
						component: {
							template: `<database />`,
							components: {
								database,
							},
						},
						ports: {
							groups: {
								top: {
									position: 'top',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
								bottom: {
									position: 'bottom',
									attrs: {
										circle: {
											r: 4,
											magnet: true,
											stroke: '#C2C8D5',
											strokeWidth: 1,
											fill: '#fff',
										},
									},
								},
							},
						},
					},
					true
				)

				Graph.registerEdge(
					'dag-edge',
					{
						inherit: 'edge',
						attrs: {
							line: {
								stroke: '#C2C8D5',
								strokeWidth: 2,
								targetMarker: {
									name: 'block',
									width: 12,
									height: 8,
								},
							},
						},
					},
					true
				)

				Graph.registerEdge(
					'arrow',
					{
						markup: [
							{
								tagName: 'path',
								selector: 'wrap',
								attrs: {
									fill: 'none',
									cursor: 'pointer',
									stroke: 'transparent',
								},
							},
							{
								tagName: 'path',
								selector: 'line',
								attrs: {
									fill: 'none',
									'pointer-events': 'none',
								},
							},
							// {
							// 	tagName: 'path',
							// 	groupSelector: 'arrow',
							// 	selector: 'arrow1',
							// },
							// {
							// 	tagName: 'path',
							// 	groupSelector: 'arrow',
							// 	selector: 'arrow2',
							// },
							// {
							// 	tagName: 'path',
							// 	groupSelector: 'arrow',
							// 	selector: 'arrow3',
							// },
						],
						attrs: {
							wrap: {
								connection: true,
								strokeWidth: 10,
								strokeLinejoin: 'round',
							},
							line: {
								connection: true,
								stroke: '#333333',
								strokeWidth: 2,
								strokeLinejoin: 'round',
								targetMarker: 'classic',
							},
							arrow: {
								d: 'M 0 -10 10 0 0 10 z',
								fill: '#ffffff',
								stroke: '#333333',
								pointerEvents: 'none',
							},
							arrow1: {
								// atConnectionRatio: 将边中的指定元素移动到指定比例 [0, 1] 位置处，并自动旋转元素，
								// 使其方向与所在位置边的斜率保持一致。教程 https://x6.antv.vision/zh/docs/api/registry/attr#atconnectionratiokeepgradient
								atConnectionRatio: 0.25,
							},
							arrow2: {
								atConnectionRatio: 0.5,
							},
							arrow3: {
								atConnectionRatio: 0.75,
							},
						},
					},
					true
				)

				Graph.registerConnector(
					'algo-connector',
					(s, e) => {
						const offset = 4
						const deltaY = Math.abs(e.y - s.y)
						const control = Math.floor((deltaY / 3) * 2)

						const v1 = { x: s.x, y: s.y + offset + control }
						const v2 = { x: e.x, y: e.y - offset - control }

						return Path.normalize(
							`M ${s.x} ${s.y}
	          L ${s.x} ${s.y + offset}
	          C ${v1.x} ${v1.y} ${v2.x} ${v2.y} ${e.x} ${
								e.y - offset
							}
	          L ${e.x} ${e.y}
	         `
						)
					},
					true
				)

				const graph = new Graph({
					grid: {
						size: 10,
						visible: true,
						type: 'dot', // 'dot' | 'fixedDot' | 'mesh'
						args: {
							color: '#a05410', // 网格线/点颜色
							thickness: 1, // 网格线宽度/网格点大小
						},
					},
					background: {
						color: '#fffbe6', // 设置画布背景颜色
					},
					container: document.getElementById('draw-cot'),
					panning: {
						enabled: true,
						eventTypes: ['leftMouseDown', 'mouseWheel'],
					},
					mousewheel: {
						enabled: true,
						modifiers: 'ctrl',
						factor: 1.1,
						maxScale: 1.5,
						minScale: 0.5,
					},
					highlighting: {
						magnetAdsorbed: {
							name: 'stroke',
							args: {
								attrs: {
									fill: '#fff',
									stroke: '#31d0c6',
									strokeWidth: 4,
								},
							},
						},
					},
					connecting: {
						snap: true,
						enabled: true, // 允许节点之间嵌套
						router: 'manhattan',
						// allowBlank: false,
						allowBlank: true, // 是否允许连接到画布空白位置的点, 默认为true
						allowLoop: false, // 是否允许连接到画布空白位置的点
						highlight: true,
						// connector: 'algo-connector',
						connectionPoint: 'anchor',
						anchor: 'center',
						validateMagnet({ magnet }) {
							// return magnet.getAttribute('port-group') !== 'top'

							// 限制连线配置
							return true
						},
						createEdge() {
							return graph.createEdge({
								// shape: 'dag-edge',
								shape: 'edge',
								attrs: {
									line: {
										strokeDasharray: '5 5',
										targetMarker: {
											name: 'block',
											width: 12,
											height: 8,
										},
									},
								},
								zIndex: -1,
							})
						},
						// 停止拖动边进行验证的边是否生效，返回false,边会被清除
						validateEdge({ edge, type, previous }) {
							console.log(
								edge,
								type,
								previous,
								'edge, type, previous'
							)
							// 触发边右键菜单功能
							let target = edge.getTargetPoint()
							console.log(target, '目标点')
							vm.showContextMenu = true
							vm.$nextTick(() => {
								vm.$refs.menuBar.initFn(
									target.x,
									target.y,
									{ type: 'edge', item: edge }
								)
							})
							return true
						},
					},
					selecting: {
						enabled: true,
						multiple: true,
						rubberEdge: true,
						rubberNode: true,
						modifiers: 'shift',
						rubberband: true,
					},
					keyboard: true,
					clipboard: true,
					history: true,
				})
				this.graph = graph

				graph.on(
					'edge:contextmenu',
					({ e, x, y, edge, view }) => {
						console.log(e, x, y, edge, view, '边右键事件')
						this.showContextMenu = true
						this.$nextTick(() => {
							this.$refs.menuBar.initFn(
								e.offsetX,
								e.offsetY,
								{ type: 'edge', item: edge }
							)
						})
					}
				)

				graph.on(
					'node:contextmenu',
					({ e, x, y, node, view }) => {
						this.showContextMenu = true

						this.$nextTick(() => {
							// this.$refs.menuBar.setItem({ type: 'node', item: node })
							const p = graph.localToPage(x, y)
							this.$refs.menuBar.initFn(p.x, p.y, {
								type: 'node',
								item: node,
							})
						})
					}
				)

				// 注册节点点击事件
				graph.on('node:click', () => {
					// 弹出右侧配置节点抽屉
					this.$refs.rightDrawer.rtlDrawer = true
				})

				graph.on('edge:connected', ({ edge }) => {
					const source = graph.getCellById(edge.source.cell)
					const target = graph.getCellById(edge.target.cell)

					// 只允许输入
					if (target.data.type == 'output') {
						return graph.removeEdge(edge.id)
					}

					// 只允许输出
					if (source.data.type == 'onlyIn') {
						return graph.removeEdge(edge.id)
					}

					// 如果线源头的一端链接桩只允许输入
					if (/in/.test(edge.source.port)) {
						return graph.removeEdge(edge.id)
					}

					// 目标一端链接桩只允许输出
					if (/out/.test(edge.target.port)) {
						return graph.removeEdge(edge.id)
					}

					if (source.data.type == 'condition') {
						console.log(source)
						console.log(target)
						console.log(edge)
						if (
							target.data.t === edge.id ||
							target.data.f === edge.id
						) {
							return graph.removeEdge(edge.id)
						}
						this.$refs.dialogCondition.visible = true
						this.$refs.dialogCondition.init(
							source.data,
							edge
						)
					}

					edge.attr({
						line: {
							strokeDasharray: '',
						},
					})
				})

				graph.on('node:change:data', ({ node }) => {
					const edges = graph.getIncomingEdges(node)
					const { status } = node.getData()
					edges?.forEach((edge) => {
						if (status === 'running') {
							edge.attr('line/strokeDasharray', 5)
							edge.attr(
								'line/style/animation',
								'running-line 30s infinite linear'
							)
						} else {
							edge.attr('line/strokeDasharray', '')
							edge.attr('line/style/animation', '')
						}
					})
				})
			},
			async showNodeStatus(statusList) {
				const status = statusList.shift()
				status?.forEach((item) => {
					const { id, status } = item
					const node = this.graph.getCellById(id)
					const data = node.getData()
					node.setData({
						...data,
						status: status,
					})
				})
				this.timer = setTimeout(() => {
					this.showNodeStatus(statusList)
				}, 300)
			},
			// 初始化节点/边
			init(data = []) {
				const cells = []
				data.forEach((item) => {
					if (item.shape === 'dag-edge') {
						cells.push(this.graph.createEdge(item))
					} else {
						delete item.component
						cells.push(this.graph.createNode(item))
					}
				})
				this.graph.resetCells(cells)
			},
			zoomFn(num) {
				this.graph.zoom(num)
			},
			centerFn() {
				const num = 1 - this.graph.zoom()
				num > 1
					? this.graph.zoom(num * -1)
					: this.graph.zoom(num)
				this.graph.centerContent()
			},
			startFn(item) {
				this.timer && clearTimeout(this.timer)
				this.init(item || DataJson)
				this.showNodeStatus(
					Object.assign([], nodeStatusList)
				)
				this.graph.centerContent()
			},
			createMenuFn() {},
			keyBindFn() {
				// copy cut paste
				this.graph.bindKey(['meta+c', 'ctrl+c'], () => {
					const cells = this.graph.getSelectedCells()
					if (cells.length) {
						this.graph.copy(cells)
					}
					return false
				})
				this.graph.bindKey(['meta+x', 'ctrl+x'], () => {
					const cells = this.graph.getSelectedCells()
					if (cells.length) {
						this.graph.cut(cells)
					}
					return false
				})
				this.graph.bindKey(['meta+v', 'ctrl+v'], () => {
					if (!this.graph.isClipboardEmpty()) {
						const cells = this.graph.paste({ offset: 32 })
						this.graph.cleanSelection()
						this.graph.select(cells)
					}
					return false
				})

				// undo redo
				this.graph.bindKey(['meta+z', 'ctrl+z'], () => {
					if (this.graph.history.canUndo()) {
						this.graph.history.undo()
					}
					return false
				})
			},
			saveFn() {
				localStorage.setItem(
					'x6Json',
					JSON.stringify(this.graph.toJSON({ diff: true }))
				)
			},
			loadFn() {
				this.timer && clearTimeout(this.timer)
				const x6Json = JSON.parse(
					localStorage.getItem('x6Json')
				)

				this.startFn(x6Json.cells)
			},
			lockFn() {
				this.isLock = !this.isLock
				if (this.isLock) {
					this.graph.enablePanning()
					this.graph.enableKeyboard()
				} else {
					this.graph.disablePanning()
					this.graph.disableKeyboard()
				}
			},
			contextMenuFn(type, node) {
				switch (type) {
					case 'remove':
						if (node.type == 'edge') {
							this.graph.removeEdge(node.item.id)
						} else if (node.type == 'node') {
							this.graph.removeNode(node.item.id)
						}
						break
					case 'source':
						this.$refs.dialogMysql.visible = true
						this.$refs.dialogMysql.init(node)
						break
				}

				this.showContextMenu = false
			},

			showDrawerFn() {
				this.$refs.drawer.visible =
					!this.$refs.drawer.visible
			},
			addNode(option) {
				const p = this.graph.pageToLocal(option.x, option.y)
				this.graph.addNode(Object.assign({}, option, p))
			},
		},
	}
</script>

<style>
	body {
		box-sizing: border-box;
		margin: 0;
	}

	header {
		display: flex;
		justify-content: flex-end;
		width: 100%;
		height: 50px;
	}

	header i {
		margin: 8px;
		font-size: 30px;
	}

	#app {
		font-family: 'Avenir', Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: #2c3e50;
		margin-top: 60px;
	}

	.my-selecting {
		border: 1px solid red;
		display: block;
		z-index: 0;
	}

	@keyframes running-line {
		to {
			stroke-dashoffset: -1000;
		}
	}

	@keyframes spin {
		from {
			transform: rotate(0deg);
		}
		to {
			transform: rotate(360deg);
		}
	}

	.x6-node-selected .node {
		border-color: #1890ff;
		border-radius: 2px;
		box-shadow: 0 0 0 4px #d4e8fe;
	}

	.x6-node-selected .node.success {
		border-color: #52c41a;
		border-radius: 2px;
		box-shadow: 0 0 0 4px #ccecc0;
	}

	.x6-node-selected .node.failed {
		border-color: #ff4d4f;
		border-radius: 2px;
		box-shadow: 0 0 0 4px #fedcdc;
	}

	.x6-edge:hover path:nth-child(2) {
		stroke: #1890ff;
		stroke-width: 1px;
	}

	.x6-edge-selected path:nth-child(2) {
		stroke: #1890ff;
		stroke-width: 1.5px !important;
	}

	.section-cot {
		display: flex;
	}

	.section-cot #container {
		position: relative;
		flex: 1;
	}

	.section-cot #container #draw-cot {
		width: 100%;
		height: 100%;
	}

	::-webkit-scrollbar {
		width: 0;
	}

	@keyframes ant-line {
		to {
			stroke-dashoffset: -1000;
		}
	}

	/* 边自定义按钮效果 */
	.x6-graph-svg
		.x6-graph-svg-decorator
		.x6-cell-tools
		.x6-cell-tool-button
		circle {
		animation: all ease 0.7;
	}
	.x6-graph-svg
		.x6-graph-svg-decorator
		.x6-cell-tools
		.x6-cell-tool-button
		circle:hover {
		fill: transparent;
		stroke-width: 5px;
	}
</style>
