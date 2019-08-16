<template>

	<div>

		<h5>Transfer Numbers</h5>

		<div class="row">

			<div class="column">

				<GroupListItem v-if="fromGroup"
					:group="fromGroup"
					:removeable="true"
					:selected="true"
					@un-select="fromGroup = false"/>

				<div v-else>

					<h6>Select Group For Transfer</h6>

					<GroupList
						:groups="groups"
						:selectable="true"
						:removable="false"
						:editable="false"
						@select="fromGroup = $event"/>

				</div>

				<Loader v-if="loadingFrom" class="slight-padding-vertical"/>
				<NumberList v-else
					:numbers="parsedFromNumbers"
					:selectable="toGroup"
					:transfer="transferFrom"
					:reserve="reserve"
					@select="startTransfer('from', $event)"
					@transfer="startTransferFrom($event)"/>

			</div>

			<div class="column">

				<div v-if="!toGroup">
					<h6>Select Group For Transfer</h6>

					<GroupList
						:groups="groups"
						:selectable="true"
						:removable="false"
						:editable="false"
						@select="toGroup = $event"/>

				</div>

				<div v-else>

					<GroupListItem
						:group="toGroup"
						:removeable="true"
						:selected="true"
						@un-select="toGroup = false"/>

					<Loader v-if="loadingTo" />
					<NumberList v-else
						:numbers="parsedToNumbers"
						:selectable="true"
						:transfer="transferTo"
						:reserve="reserve"
						@select="startTransfer('to', $event)"
						@transfer="startTransferTo($event)"/>

				</div>

			</div>

		</div>

		<div class="button button-clear float-right padding-0-right"
			@click="$emit('cancel')">
			<i class="fa fa-check"></i> Done
		</div>

	</div>

</template>

<script>
import axios from 'axios';
import GroupList from '../molecules/GroupList.vue';
import GroupListItem from '../atoms/GroupListItem.vue';
import Loader from '../atoms/Loader.vue';
import NumberList from '../molecules/NumberList.vue';
import { parsePhoneNumberFromString } from 'libphonenumber-js';
export default {
	name: "ModalTransferNumbers",
	props: ['initial-from', 'groups'],
	components: { GroupList, GroupListItem, Loader, NumberList },

	data: function() {
		return {
			fromGroup: this.initialFrom.group,
			fromNumbers: this.initialFrom.numbers,
			loadingFrom: false,
			loadingTo: false,
			reserve: false,
			toGroup: false,
			toNumbers: [],
			transferTo: false,
			transferFrom: false
		}
	},

	computed: {


		parsedFromNumbers: function() {

			let numbers = [];
			if (this.fromGroup) {

				let transferNum = this.transfer;
				this.fromNumbers.forEach((number) => {

					if (number == transferNum) {
						numbers.push('transfer');
					}

					let parsedNumber = parsePhoneNumberFromString(number.number, 'US');
					if (parsedNumber) {
						numbers.push(parsedNumber.formatInternational());
					}

				});

			}

			return numbers;

		},

		parsedToNumbers: function() {

			let numbers = [];
			if (this.toGroup && this.toNumbers) {

				this.toNumbers.forEach((number) => {

					let parsedNumber = parsePhoneNumberFromString(number.number, 'US');
					if (parsedNumber) {
						numbers.push(parsedNumber.formatInternational());
					}

				});

			}

			return numbers;

		}

	},

	methods: {

		// Transfers EVERYTHING the user has selected for transfer
		bulkTransfer: function() {


		},

		// Used when user clicks on a number with the intent to transfer.  Could
		// think of this as "priming" a transfer
		startTransfer: function(side, transferNum) {

			let numbers;

			// First, which side did this come from?  We'll want to pick numbers from
			// that side, naturally
			if (side == 'from') {
				numbers = this.fromNumbers;
			} else if (side == 'to') {
				numbers = this.toNumbers;
			}

			// Next, iterate through the numbers until you find our target.
			// Once found, we'll mark it as reserved
			let _this = this;
			numbers.forEach((number, index) => {

				let parsedNumber = parsePhoneNumberFromString(number.number);
				if (parsePhoneNumberFromString(number.number).formatInternational() == transferNum) {

					if (side == 'from') {
						_this.transferFrom = transferNum;
					} else if (side == 'to') {
						_this.transferTo = transferNum;
					}

					_this.reserve = {
						index,
						number,
						transfering: false,
						transfered: false
					};
				}

			});

		},

		// Handles transfer events bubbling up from the from group's number list,
		// we take this to mean that we are transfering a number to that group.
		startTransferFrom: function(number) {

			this.reserve.transfering = true;
			axios.put(`${process.env.VUE_APP_ENDPOINT}/phones/${number.id}/from/${this.toGroup.id}/to/${this.fromGroup.id}`)
			.then((response) => {

				// Update from group numbers such that this number exists in their array
				this.fromNumbers.push(number);

				// And remove number from to numbers array
				for(let i = 0; i < this.toNumbers.length; i ++) {
					if (this.toNumbers[i] == number) {
						this.toNumbers.splice(i, 1);
						break;
					}
				}

				this.reserve = false;
				this.transfer = false;

			})
			.catch((error) => {

			});

		},

		startTransferTo: function(number) {

			this.reserve.transfering = true;
			axios.put(`${process.env.VUE_APP_ENDPOINT}/phones/${number.id}/from/${this.fromGroup.id}/to/${this.toGroup.id}`)
			.then((response) => {

				this.toNumbers.push(number);

				for(let i = 0; i < this.fromNumbers.length; i ++) {
					if (this.fromNumbers[i] == number) {
						this.fromNumbers.splice(i, 1);
						break;
					}
				}

				this.reserve = false;
				this.transfer = false;

			})
			.catch((error) => {

			});

		}

	},

	watch: {

		fromGroup: function() {

			if (this.fromGroup.id) {

				this.loadingFrom = true;

				axios.get(`${process.env.VUE_APP_ENDPOINT}/groups/${this.fromGroup.id}`)
				.then((response) => {

					this.loadingFrom = false;

					let numbers = [];
					let included = response.data.included;
					included.forEach((number) => {

						console.log(number);
						numbers.push({
							id: number.id,
							number: number.attributes.number
						});
					});

					this.fromNumbers = numbers;

				});
			}
		},

		// Fetches numbers for that group anytime it changes
		toGroup: function() {

			if (this.toGroup.id) {

				this.loadingTo = true;
				axios.get(`${process.env.VUE_APP_ENDPOINT}/groups/${this.toGroup.id}`)
				.then((response) => {

					this.loadingTo = false;

					// Alright, we have our numbers
					let numbers = [];
					let included = response.data.included;
					included.forEach((number) => {

						console.log(number);
						numbers.push({
							id: number.id,
							number: number.attributes.number
						});
					});

					this.toNumbers = numbers;

				})
				.catch((error) => {

				});

			}

		}

	}

}
</script>
