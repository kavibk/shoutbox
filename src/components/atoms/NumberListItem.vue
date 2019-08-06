<!-- Component for rendering a single phone number within the NumberList
  component.  Accepts as a prop a string containing a formatted phone number,
  as well as another prop for whether or not this is a duplicate. -->
<template>
  <li>

    <div class="list-content reserve" v-if="reserve"
      @click="$emit('transfer', reserve.number)">
      Transfer
    </div>

    <div class="list-content" :class="{selectable, transfer}" v-else
      @click="selectable ? $emit('select', number) : ''">
      {{ number }}
    </div>

  </li>
</template>

<script>
export default {
  name: "NumberListItem",
  props: ['number', 'duplicate', 'selectable', 'transfer', 'reserve']
}
</script>

<style lang="scss" scoped>
@import '../../styles/colors';
.selectable, .reserve {
  cursor: pointer;
}

.transfer, .reserve {
  background-color: $white;
  border: 1px solid $kavi-orange;
  color: $kavi-orange;
  text-transform: uppercase;
}
</style>
