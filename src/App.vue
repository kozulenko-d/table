<template>
	<div id="app">
		<header>
			<div class="logo">
				<span>made with</span>
				<img src="./assets/logo.png">
			</div>
		</header>
		<main>
			<div class="search-box">
				<div class="search-row">
					<div class="input-box">
						<label for="name">Name:</label>
						<input id="name" type="text" placeholder="name" v-model="nameFilter">
					</div>
					<div class="input-box">
						<label for="location">Location:</label>
						<input id="location" type="text" placeholder="location" v-model="locationFilter">
					</div>
					<div class="input-box">
						<label for="currency">Currency:</label>
						<input type="number" id="currency" placeholder="currency" v-model="currencyFilter">
					</div>
					<div class="input-box">
						<input type="radio" name="curSearch" id="curSearchLess" value="less" v-model="currOption">
						<label for="curSearchLess">less than</label><br>
						<input type="radio" name="curSearch" id="curSearchMore" value="more" v-model="currOption">
						<label for="curSearchMore">more than</label><br>
						<input type="radio" name="curSearch" id="curSearchExact" value="exact" v-model="currOption">
						<label for="curSearchExact">exact amount</label><br>
					</div>
				</div>
			</div>
			<p class="left-text">Rows shown: {{ this.filteredRows.length }}</p>
			<table>
				<thead>
					<tr>
						<th @click="sortTab('id')">
							<head-cell :cls="sortWay" :active="sort" sort="id"></head-cell>
						</th>
						<th @click="sortTab('name')">
							<head-cell :cls="sortWay" :active="sort" sort="name"></head-cell>
						</th>
						<th @click="sortTab('location')">
							<head-cell :cls="sortWay" :active="sort" sort="location"></head-cell>
						</th>
						<th @click="sortTab('currency')">
							<head-cell :cls="sortWay" :active="sort" sort="currency"></head-cell>
						</th>
						<th>
							Detail
						</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(r, i) in filteredRows" :key="'row' + i">
						<td @dblclick="edit(r,i,'id')">
							<input
								type="text"
								v-if="editField === 'id' + i"
								v-model="r.id"
								@keyup.enter="editField = ''"
								@focusout="editField=''"
							>
							<span v-else>{{ r.id }}</span>
						</td>
						<td @dblclick="edit(r,i,'name')">
							<input
								type="text"
								v-if="editField === 'name' + i"
								v-model="r.name"
								@keyup.enter="editField = ''"
								@focusout="editField=''"
							>
							<span v-else>{{ r.name }}</span>
						</td>
						<td @dblclick="edit(r,i,'location')">
							<input
								type="text"
								v-if="editField === 'location' + i"
								v-model="r.location"
								@keyup.enter="editField = ''"
								@focusout="editField=''"
							>
							<span v-else>{{ r.location }}</span>
						</td>
						<td @dblclick="edit(r,i,'currency')">
							<input
								type="number"
								v-if="editField === 'currency' + i"
								v-model="r.currency"
								@keyup.enter="editField = ''"
								@focusout="editField=''"
							>
							<span v-else>{{ +r.currency }}</span>
						</td>
						<td>
							<img
								@click="showPopup(r)"
								src="https://cdn4.iconfinder.com/data/icons/standard-free-icons/139/Find01-512.png">
						</td>
					</tr>
				</tbody>
				<tfoot>
					<tr>
						<td colspan="3">Total:</td>
						<td>{{ currencySum }}</td>
						<td></td>
					</tr>
				</tfoot>
			</table>
		</main>
		<div class="popup" v-if="detailBox" @click.self="detailBox = null">
			<div class="popupbody">
				<p>Information detail:</p>
				<p>Id: {{ detailBox.id }}</p>
				<p>Name: {{ detailBox.name }}</p>
				<p>Location: {{ detailBox.location }}</p>
				<p>Currency: {{ detailBox.currency }}</p>
				<div @click="detailBox = null">Got it</div>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'app',
		data () {
			return {
				rows: this.$store.state.test,
				nameFilter: '',
				locationFilter: '',
				currencyFilter: '',
				currOption: 'exact',
				sort: '',
				sortWay: 'desc',
				editField: '',
				detailBox: null
			}
		},
		computed: {
			filteredRows() {
				if (!this.rows) return;
				let filtered = this.rows;

				if (this.nameFilter) {
					filtered = filtered.filter(r => r.name.toLowerCase().includes(this.nameFilter.toLowerCase()));
				}
				if (this.locationFilter) {
					filtered = filtered.filter(r => r.location.toLowerCase().includes(this.locationFilter.toLowerCase()));
				}
				if (this.currencyFilter && this.currOption === 'exact') {
					filtered = filtered.filter(r => r.currency === +this.currencyFilter);
				} else if (this.currencyFilter && this.currOption === 'less') {
					filtered = filtered.filter(r => r.currency < +this.currencyFilter);
				} else if (this.currencyFilter && this.currOption === 'more') {
					filtered = filtered.filter(r => r.currency > +this.currencyFilter);
				}

				if (this.sort) {
					let desc = this.sortWay === 'desc' ? 1 : -1;
					filtered = filtered.sort((f, g) => {
						if (f[this.sort] < g[this.sort]) return desc;
						if (f[this.sort] > g[this.sort]) return -desc;
						return 0;
					})
				}

				return filtered;
			},
			currencySum() {
				return this.filteredRows.reduce((sum,cur) => {
					return sum + +cur.currency;
				}, 0);
			}
		},
		methods: {
			sortTab(s) {
				this.sort = s;
				this.sortWay = this.sortWay === 'desc' ? 'asc' : 'desc';
			},
			edit(r,i,f) {
				this.editField = f + i;
			},
			showPopup(r) {
				this.detailBox = r;
			}
		}
	}
</script>

<style lang="scss">
	@import 'var.scss';

	* {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}
	body {
		background: $bgColor;
	}
	#app {
		font-family: 'Avenir', Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: $primeColor;
	}
	header {
		padding: 4px 8px;
		height: 40px;
		width: 100%;
		.logo {
			float: left;
			position: relative;
			img {
				height: 36px;
			}
			span {
				line-height: 40px;
				font-size: 1.4em;
				position: relative;
				bottom: 11px;
			}
		}
	}
	.search-box {
		margin: 16px;
		.search-row {
			text-align: left;
			.input-box {
				display: inline-block;
				position: relative;
				width: 24%;
				label {
					position: absolute;
					left: 4px;
					top: -100%;
				}
				input[type="text"], input[type="number"] {
					height: 24px;
					font-size: 17px;
					width: 90%;
				}
				input[type="radio"] {
					right: 0;
					position: relative;
					& ~ label {
						position: relative;
						left: 0;
						top: 0;
					}
				}
			}
		}
	}
	main {
		padding-top: 8px;
		width: 90%;
		max-width: 1000px;
		margin: 0 auto;
		table {
			width: 100%;
			border-collapse: collapse;
			margin-bottom: 24px;
			th,td {
				border: 1px solid #ccc;
				min-width: 88px;
				max-width: 400px;
			}
			td {
				font-weight: bold;
				text-overflow: ellipsis;
				white-space: nowrap;
				overflow: auto;
				input {
					width: 88%;
				}
				img {
					width: 16px;
					height: 16px;
					display: block;
					margin: 0 auto;
					cursor: pointer;
				}
			}
			thead {
				background-color: lightblue;
			}
			tbody, tfoot {
				background-color: #fafafa;
				tr:hover {
					background-color: #dedede;
				}
			}
			tfoot {
				font-size: 1.2em;
				td:first-child {
					text-align: right;
					padding-right: 16px;
				}
			}
		}
	}
	.left-text{
		text-align: left;
	}
	.popup {
		position: fixed;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: rgba(200,200,200,0.4);
		.popupbody {
			width: 360px;
			height: 200px;
			background-color: $bgColor;
			margin: 80px auto 0;
			border: 1px solid #fafafa;
			position: relative;
			p {
				margin: 8px 0;
				&:not(:first-of-type) {
					text-align: left;
					margin-left: 8px;
				}
			}
			div {
				position: absolute;
				left: calc(50% - 40px);
				bottom: 20px;
				width: 80px;
				height: 24px;
				background-color: royalblue;
				color: #fafafa;
				font-weight: bold;
				border-radius: 4px;
				margin: 0 auto;
				line-height: 24px;
				cursor: pointer;
			}
		}
	}
</style>
