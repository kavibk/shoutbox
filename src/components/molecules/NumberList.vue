<!-- A template for rendering a list of numbers.  Accepts as a prop an array
  containing strings that have some kind of valid phone number. -->
<template>
  <ul>

    <NumberListItem v-for="(number, index) in parsedNumbers"
      :number="numberOrReserve(number, index)"
      :transfer="number == transfer"
      :reserve="index === reserve.index && !transfer ? reserve : false"
      :selectable="selectable"
      @select="$emit('select', $event)"
      @transfer="$emit('transfer', $event)"/>

    <NumberListItem v-if="reserve.index >= 0 && !transfer"
      :number="tailNumber"
      :reserve="tailReserve"
      @transfer="$emit('transfer', $event)"/>

  </ul>
</template>

<script>
import { parsePhoneNumberFromString } from 'libphonenumber-js';
import NumberListItem from '../atoms/NumberListItem.vue';
export default {
  name: "NumberList",
  props: ['numbers', 'selectable', 'transfer', 'reserve'],
  components: {
    NumberListItem
  },

  computed: {

    // Takes numbers prop and formats each number within to look like an
    // "international style" number.  Returns formatted numbers
    parsedNumbers: function() {

      let numbers = [];

      this.numbers.forEach((number) => {
        const phoneNumber = parsePhoneNumberFromString(number);
        numbers.push(
          phoneNumber.format('INTERNATIONAL')
        )
      });

      return numbers;

    },

    tailNumber: function() {

      if (this.reserve > this.numbers.length) {
        return false;
      }

      return this.parsedNumbers[this.parsedNumbers.length - 1];

    },

    tailReserve: function() {

      if (this.reserve > this.numbers.length || this.reserve !== false && this.numbers.length == 0) {
        return this.reserve;
      }

      return false;

    }

  },

   methods: {

     // The number list is a bit tricky.  We don't always simply iterate through
     // numbers and add them to the list.  We may need to reserve a spot as a
     // "slot" to transfer a number into from a different group.  If we do that,
     // then from that point on we need to shift by -1 the number that should
     // be inserted into that list item
     numberOrReserve: function(number, index) {

       if (this.transfer || this.reserve === undefined || this.reserve === false) {
         return number;
       }

       if (this.reserve !== false && this.reserve.index >= 0 && index < this.reserve) {
         return number;
       } else if (this.reserve !== false && this.reserve.index >= 0 && index > this.reserve) {
         return this.parsedNumbers[index - 1];
       }

       return '';

     }

   }
}
</script>
