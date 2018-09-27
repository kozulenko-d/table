<template>
	<div>
		<span>{{ sort }} <span v-if="active === sort" :class="cls"></span></span>
	</div>
</template>

<script>
	export default {
		props : {
			'sort' : {
				type: String,
				required: true
			},
			'cls' : {
				type: String
			},
			'active' : {
				type: String,
				required: true
			}
		},
		data() {
			return {}
		}
	}
</script>

<style lang="scss" scoped>
	@import '../var.scss';

	span {
		text-transform: capitalize;
		cursor: pointer;
		& > span {
			position: relative;
			&::after {
				display: block;
				content:'';
				position: absolute;
				left: 2px;
				top: 34%;
				width: 0;
				height: 0;
				border-style: solid;
			}
			&.asc::after {
				border-width: 0 6px 8px 6px;
				border-color: transparent transparent $primeColor transparent;
			}
			&.desc::after {
				border-width: 8px 6px 0 6px;
				border-color: $primeColor transparent transparent transparent;
			}
		}
	}
</style>