<!-- Section of the modal for displaying a list of groups.  From here, user's
	have the option to select a group to add to a shout or modify a group. -->
<template>
	<div>

		<GroupList
			:groups="groups"
			:selected="selected"
			:selectable="true"
			:editable="true"
			:removeable="false"
			@select="selected.push($event)"
			@un-select="unselect($event)"
			@edit="$emit('edit', $event)"/>

		<button class="button button-clear" @click="$emit('add-group')" v-if="!adding">
			<i class="fas fa-plus-circle"></i> Add New Group
		</button>

		<button class="button button-clear" @click="$emit('confirm', selected)" v-if="selected.length">
			Confirm Groups
		</button>

	</div>
</template>

<script>
import GroupList from '../molecules/GroupList.vue';
import GroupListItem from '../atoms/GroupListItem.vue';
export default {
	name: "ModalGroupList",
	props: ['adding', 'groups'],
	components: { GroupList, GroupListItem },

	data: function() {
		return {
			selected: []
		}
	},

	methods: {

		unselect: function(group) {

			for(let i = 0; i < this.selected.length; i ++) {
				if (this.selected[i] == group) {
					this.selected.splice(i, 1);
					return;
				}
			}

		}
	}
}
</script>

<style lang="scss" scoped>
.button {
	margin-left: 0;
	padding-left: 0;
}
</style>
