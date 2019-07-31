<!-- A list of groups.  Besides listing groups, can also expose a number of
	controls for doing things to them. -->
<template>
	<ul>
		<GroupListItem v-for="group in groups"
			:group="group"
			:selected="selected && selected.includes(group)"
			:selectable="selectable"
			:editable="editable"
			:removeable="removeable"
			@select="$emit('select', $event)"
			@un-select="$emit('un-select', $event)"
			@edit="$emit('edit', group)"/>
	</ul>
</template>

<script>
import GroupListItem from '../atoms/GroupListItem.vue';
export default {
	name: "GroupList",
	props: ['groups', 'selected', 'selectable', 'editable', 'removeable'],
	components: { GroupListItem }
}
</script>
