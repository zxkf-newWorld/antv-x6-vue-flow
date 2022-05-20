<!--
 * @Author: hp
 * @Date: 2022-05-10 13:22:28
 * @LastEditTime: 2022-05-10 13:46:15
 * @LastEditors: your name
 * @Description: 页面头部
 * @FilePath: /vue-x6-flow/src/components/header.vue
-->
<template>
	<div class="page-header">
		<!-- 页面logo -->
		<img src="../assets/nav_logo_solar.png" alt="" />
		<!-- 流程名称+最新更新日期 -->
		<div class="title">
			<span>自定义事件流程图</span>
			<p>最后修改：2022-05-10</p>
		</div>
	</div>
</template>

<script>
	export default {
		name: '',
		data() {
			return {}
		},
	}
</script>

<style scoped>
	.page-header {
		width: 100%;
		height: 70px;
		border-bottom: 1px solid #ccc;
	}
	.page-header img {
		padding: 0 10px;
		vertical-align: middle;
	}
	.title {
		display: inline-block;
		vertical-align: middle;
	}
	.title p {
		margin: 0;
	}
</style>
