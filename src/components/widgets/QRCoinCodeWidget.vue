<template>
	<div id="JSEA-QRMask" v-if="activeCode">
		<div>
			<div class="popupHeader">
				Export QR Coin Code
				<i class="fa fa-close"></i>
			</div>
			<div class="popupContent">
				<ScrollWidget>
					<div style="padding: 10px;">
						<div style="position:relative;">
							<i v-if="coinCodePos !== 0" class="fa fa-angle-left" v-on:click="prevCoinCode"></i>
							<i v-if="((availableCoins) && ((coinCodePos+1) !== availableCoins.length))" class="fa fa-angle-right" v-on:click="nextCoinCode"></i>
							<div id="JSEA-QRBGImage"></div>
							<qriously v-if="activeCode" v-bind="{foregroundAlpha:1, backgroundAlpha:0}" :value="activeCode" foreground="#0d152c" :size="250" />
						</div>

						<div class="coinInfoLabel">
							<b>Amount:</b>
							<div style="display:flex; padding: 4px 8px; align-items:center;">
								<Coin :coinClass="{gold:activeCodeValue >= 1, silver:activeCodeValue < 1, small: true}" />
								{{activeCodeValue}}
							</div>
						</div>
						<div class="coinInfoLabel">
							<b>Export Date:</b>
							<div style="display:flex; padding: 4px 8px;">
								{{activeCodeExportDate}}
							</div>
						</div>
						<div id="JSEA-coinCode">{{activeCode}}</div>

						<!-- Download Coin Code PDF Page -->
						<GenerateCoinPageWidget
							style="margin:20px auto"
							:buttonTxt="($store.getters.whichPlatform !== 'mobile')?'Paper Download':'Share Code'"
							v-bind="{coinData: coinData}" />
						<!-- xDownload Coin Code PDF Page -->
					</div>
				</ScrollWidget>
			</div>
		</div>
	</div>
</template>

<script>
import moment from 'moment';
import QRious from 'qrious';
import GenerateCoinPageWidget from '@/components/widgets/GenerateCoinPageWidget.vue';
import Coin from '@/components/widgets/Coin.vue';
import ScrollWidget from '@/components/widgets/ScrollWidget.vue';

/**
 * @description
 * QR Coin Code Widget
 */
export default {
	name: 'QRCoinCodeWidget',
	inheritAttrs: false,
	data() {
		const self = this;
		return {
			activeCode: '',
			activeCodeValue: '',
			activeCodeExportDate: '',
			coinCodePos: 0,
			availableCoins: [],
			coinData: {},
		};
	},
	props: {
		/**
		 * Active Coin Code Hash
		 */
		initActiveCode: {
			type: String,
			default: '',
		},
		/**
		 * Active Coin Code JSE Value
		 */
		initActiveCodeValue: {
			type: String,
			default: '',
		},
		/**
		 * Coin Code Export Date
		 */
		initActiveCodeExportDate: {
			type: String,
			default: '',
		},
		/**
		 * Active position of selected coin code
		 */
		initCoinCodePos: {
			type: Number,
			default: 0,
		},
		/**
		 * Array of all coin codes
		 */
		initAvailableCoins: {
			type: Array,
			default() {
				return [];
			},
		},
		/**
		 * Coin data obj
		 */
		initCoinData: {
			type: Object,
			default() {
				return {};
			},
		},
	},
	components: {
		GenerateCoinPageWidget,
		Coin,
		ScrollWidget,
	},
	watch: {
		initActiveCode(update) {
			const self = this;
			self.activeCode = update;
		},
		initActiveCodeValue(update) {
			const self = this;
			self.activeCodeValue = update;
		},
		initActiveCodeExportDate(update) {
			const self = this;
			self.activeCodeExportDate = update;
		},
		initCoinCodePos(update) {
			const self = this;
			self.coinCodePos = update;
			self.coinData = self.availableCoinData;
		},
		initCoinData(update) {
			const self = this;
			self.coinData = update;
		},
		initAvailableCoins(update) {
			const self = this;
			self.availableCoins = update;
		},
	},
	methods: {
		/**
		 * Generate previous available coin QR code
		 *
		 * @param {Object} e - click event
		 * @returns nothing
		 * @public
		 */
		prevCoinCode(e) {
			const self = this;
			e.preventDefault();
			e.stopPropagation();
			self.coinCodePos -= 1;
			const coin = self.availableCoins[self.coinCodePos];
			self.coinData = coin;
			self.activeCode = coin.coinCode;
			self.activeCodeValue = coin.value;
			self.activeCodeExportDate = moment(coin.ts).format('DD/MM/YY HH:mm:ss');
		},
		/**
		 * Generate next available coin QR code
		 *
		 * @param {Object} e - click event
		 * @returns nothing
		 * @public
		 */
		nextCoinCode(e) {
			const self = this;
			e.preventDefault();
			e.stopPropagation();
			self.coinCodePos += 1;
			const coin = self.availableCoins[self.coinCodePos];
			self.coinData = coin;
			self.activeCode = coin.coinCode;
			self.activeCodeValue = coin.value;
			self.activeCodeExportDate = moment(coin.ts).format('DD/MM/YY HH:mm:ss');
		},
	},
};
</script>
