<template>

	<div>

		<h5>Transfer Numbers</h5>

		<div class="row">

			<div class="column">

				<GroupListItem
					:group="fromGroup.group"
					:selected="true"/>

				<NumberList
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

					<NumberList
						:numbers="parsedToNumbers"
						:selectable="true"
						:transfer="transferTo"
						:reserve="reserve"
						@select="startTransfer('to', $event)"
						@transfer="startTransferTo($event)"/>

				</div>

			</div>

		</div>

	</div>

</template>

<script>
import axios from 'axios';
import GroupList from '../molecules/GroupList.vue';
import GroupListItem from '../atoms/GroupListItem.vue';
import NumberList from '../molecules/NumberList.vue';
import { parsePhoneNumberFromString } from 'libphonenumber-js';
export default {
	name: "ModalTransferNumbers",
	props: ['from-group', 'groups'],
	components: { GroupList, GroupListItem, NumberList },

	data: function() {
		return {
			toGroup: false,
			toNumbers: false,
			transferTo: false,
			transferFrom: false,
			reserve: false
		}
	},

	computed: {


		parsedFromNumbers: function() {

			let numbers = [];
			if (this.fromGroup) {

				let transferNum = this.transfer;
				this.fromGroup.numbers.forEach((number) => {

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

		// Initiates a transfer.
		startTransfer: function(side, transferNum) {


			let numbers;
			if (side == 'from') {
				numbers = this.fromGroup.numbers;
			} else if (side == 'to') {
				numbers = this.toNumbers;
			}

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
						number
					};
				}

			});

		},

		// Handles transfer events bubbling up from the from group's number list,
		// we take this to mean that we are transfering a number to that group.
		startTransferFrom: function(number) {

			axios.put(`http://localhost:8088/phones/${number.id}/from/${this.toGroup.id}/to/${this.fromGroup.group.id}`)
			.then((response) => {
				// TODO:
			})
			.catch((error) => {

			});

		},

		startTransferTo: function(number) {

			axios.put(`http://localhost:8088/phones/${number.id}/from/${this.fromGroup.group.id}/to/${this.toGroup.id}`)
			.then((response) => {
				// TODO
			})
			.catch((error) => {

			});

		}

	},

	watch: {

		// Fetches numbers for that group anytime it changes
		toGroup: function() {

			let _this = this;
			if (this.toGroup.id) {

				axios.get(`http://localhost:8088/groups/${this.toGroup.id}`)
				.then((response) => {

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

					_this.toNumbers = numbers;

				})
				.catch((error) => {

				});

			}

		}

	}

}
</script>
