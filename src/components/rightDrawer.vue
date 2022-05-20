<!--
 * @Author: hp
 * @Date: 2022-05-10 13:07:30
 * @LastEditTime: 2022-05-10 13:21:27
 * @LastEditors: your name
 * @Description: 右侧组件配置抽屉
 * @FilePath: /vue-x6-flow/src/components/rightDrawer.vue
-->
<template>
	<div class="right-drawer">
		<el-drawer
			v-if="rtlDrawer"
			title="组件信息"
			:modal="false"
			:wrapperClosable="false"
			:visible.sync="rtlDrawer"
			:direction="'rtl'"
			:modal-append-to-body="false"
			@close="handleRightDrawerClose"
		>
		</el-drawer>
	</div>
</template>

<script>
	export default {
		name: '',
		data() {
			return {
				rtlDrawer: false,
			}
		},
		methods: {
			handleRightDrawerClose() {
				this.rtlDrawer = false
			},
		},
	}
</script>

<style scoped>
	.right-drawer ::v-deep .el-drawer__wrapper {
		position: absolute;
		width: 300px;
		right: 0;
		left: auto;
		box-sizing: border-box;
	}

	.right-drawer ::v-deep .el-drawer {
		width: 100% !important;
	}
</style>
