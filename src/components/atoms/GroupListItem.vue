<!-- It's just a list item with event handing for groups -->
<template>
	<li>

		<a href="#" v-if="selectable"
			:class="{'inactive': selected}"
			@click.prevent="$emit('select', group)">
			<i class="fas fa-plus-circle"></i>
		</a>

		<div class="list-content" :class="{'selected': selected}">
			{{ group.name }}
			<a href="#" @click.prevent="$emit('un-select', group)" v-if="selected || removeable">
				<i class="fa fa-times"></i>
			</a>
		</div>

		<a href="#" @click.prevent="$emit('edit', group)" v-if="editable">
			<i class="fa fa-pencil"></i>
		</a>

	</li>
</template>

<script>
export default {
	name: "GroupListItem",
	props: ['group', 'selected', 'selectable', 'editable', 'removeable']
}
</script>

<style lang="scss" scoped>
@import '../../styles/colors';
.list-content {
	border: 1px solid $kavi-orange;
	background-color: $white;
	color: $kavi-orange;
	margin: 0 4px;

	&.selected {
		background-color: $kavi-orange;
		color: $white;
	}

	a {
		color: $white;
	}
}

a {
	color: $kavi-orange;

	&.inactive {
		opacity: 0;
		pointer-events: none;
	}
}
</style>
