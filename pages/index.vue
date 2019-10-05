<template>
	<div>
		<div class="container-fluid background-header-wrapper">
			<div class="container">
				<div class="row">
					<div class="col-md-12 p-0">
						<navbar></navbar>
					</div>
				</div>
			</div>
		</div>
		<div class="container">
			<div class="row">
				<div class="col-md-6">
					<div class="form-input">
						<label for="input-formatter">Mata Uang</label>
						<b-form-select
							v-model="mataUangTerpilih"
							:options="listMataUang"
						></b-form-select>
					</div>
				</div>

				<div class="col-md-6">
					<div class="form-input">
						<label for="input-nominal">Nominal</label>
						<b-form-input
							id="input-nominal"
							v-model="nominal"
							placeholder="Masukan nominal"
							aria-describedby="input-formatter-help"
							:disabled="mataUangTerpilih == null"
							:state="nominal >= 1"
						></b-form-input>
					</div>
				</div>

				<div class="col-md-12">
					<b-button
						class="mt-3"
						variant="success"
						@click="getKonversi"
						:disabled="buttonKonversi"
						>Konversikan ke Bitcoin
						<b-spinner v-if="spinner" small label="Spinning"></b-spinner
					></b-button>
				</div>

				<div class="col-md-12">
					<div class="result">
						<div class="row">
							<div class="col-md-5">
								<span>Mata Uang</span>
								<div class="box-money">
									{{ nominalCopy }}
								</div>
							</div>
							<div class="col-md-2 align-self-center text-center">
								<span class="equal-symbol">=</span>
							</div>
							<div class="col-md-5">
								<span>Bitcoin</span>
								<div class="box-money">
									{{ totalBitcoin }}
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import Navbar from '~/components/Navbar.vue';

export default {
	components: {
		Navbar
	},

	data() {
		return {
			mataUangTerpilih: {},
			listMataUang: [{ value: {}, text: 'Pilih mata uang' }],
			nominal: 0,
			totalBitcoin: 0,
			spinner: false
		};
	},

	computed: {
		nominalCopy() {
			return `${this.mataUangTerpilih.simbol || ''} ${this.nominal}` || 0;
		},
		buttonKonversi() {
			if (this.mataUangTerpilih != {} && this.nominal >= 1) {
				return false;
			} else {
				return true;
			}
		}
	},

	created() {
		this.getTicker();
	},

	methods: {
		getTicker() {
			this.$axios.$get('https://blockchain.info/ticker').then(res => {
				Object.keys(res).map((key, index) => {
					this.listMataUang.push({
						value: { singkatan: key, simbol: res[key].symbol },
						text: key
					});
				});
			});
		},
		getKonversi() {
			this.spinner = true;
			this.$axios
				.$get(
					`https://blockchain.info/tobtc?currency=${this.mataUangTerpilih.singkatan}&value=${this.nominal}`
				)
				.then(res => {
					this.totalBitcoin = res;
					this.spinner = false;
				});
		}
	}
};
</script>

<style lang="scss" scoped>
.background-header-wrapper {
	background-color: #17a2b8;
}

.form-input {
	margin-top: 2rem;
}

.result {
	margin-top: 3rem;

	.box-money {
		background-color: #17a2b8;
		padding: 1rem;
		color: #fff;
		font-weight: 600;
		font-size: 1.8em;
		margin-top: 1rem;
		border-radius: 5px;
	}

	span {
		&.equal-symbol {
			font-weight: bold;
			font-size: 2em;
			color: #17a2b8;
		}
	}
}
</style>
