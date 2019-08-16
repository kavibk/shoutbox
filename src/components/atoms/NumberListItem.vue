<!-- Component for rendering a single phone number within the NumberList
  component.  Accepts as a prop a string containing a formatted phone number,
  as well as another prop for whether or not this is a duplicate. -->
<template>
  <li>

    <div class="list-content reserve"
      v-if="typeof number == 'object' && transfer === false"
      @click="$emit('transfer', number.number)">
      Transfer
    </div>

    <div class="list-content reserve" v-else-if="typeof number == 'object' && number.transfering">
      <Loader />
    </div>

    <div class="list-content" :class="{selectable, transfer}" v-else
      @click="selectable ? $emit('select', number) : ''">
      {{ number }}

      <a href="#" v-if="removeable" @click.prevent="$emit('remove', number)">
        <i class="fa fa-times"/>
      </a>

    </div>

  </li>
</template>

<script>
import Loader from './Loader.vue';
export default {
  name: "NumberListItem",
  props: ['number', 'duplicate', 'removeable', 'selectable', 'transfer', 'transfering'],
  components: { Loader }
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
