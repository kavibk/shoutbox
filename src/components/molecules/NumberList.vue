<!-- A template for rendering a list of numbers.  Accepts as a prop an array
  containing strings that have some kind of valid phone number. -->
<template>
  <ul>

    <NumberListItem v-for="number in parsedNumbers"
      :number="number"
      :transfer="number == transfer"
      :removeable="removeable"
      :selectable="selectable"
      @select="$emit('select', $event)"
      @transfer="$emit('transfer', $event)"
      @remove="$emit('remove', $event)"/>

  </ul>
</template>

<script>
import { parsePhoneNumberFromString } from 'libphonenumber-js';
import NumberListItem from '../atoms/NumberListItem.vue';
export default {
  name: "NumberList",
  props: ['numbers', 'selectable', 'transfer', 'transfering', 'removeable', 'reserve'],
  components: {
    NumberListItem
  },

  computed: {

    // Takes numbers prop and formats each number within to look like an
    // "international style" number.  Additionally, fits in "reserved spots"
    // for number transfers.  Returns array of formatted number strings mixed
    // with objects for transfers
    parsedNumbers: function() {

      let numbers = [];

      for(let i = 0; i < this.numbers.length; i ++) {

        let number = this.numbers[i];

        if (this.reserve && i === this.reserve.index && number != this.transfer) {

          if (numbers[numbers.length -1] != this.reserve) {
            numbers.push(this.reserve);
            i --;
          }

        } else {

          const phoneNumber = parsePhoneNumberFromString(number);
          numbers.push(
            phoneNumber.format('INTERNATIONAL')
          );

        }

      }

      // We may have some leftover reserved spots.  Just put them on the end
      if (this.reserve && this.reserve.index >= this.numbers.length) {
        numbers.push(this.reserve);
      }

      return numbers;

    }

  },

   methods: {

     // The number list is a bit tricky.  We don't always simply iterate through
     // numbers and add them to the list.  We may need to reserve a spot as a
     // "slot" to transfer a number into from a different group.  If we do that,
     // then from that point on we need to shift by -1 the number that should
     // be inserted into that list item
     numberOrReserve: function(number, index) {

       // The base case is that this list doesn't have any reserved spots at all
       // in which case, just return the number
       if (this.reserve === undefined || this.reserve === false) {
         return number;
       }

       // Otherwise, suppose we do need to reserve a spot, but we haven't
       // reached the point we'd like to reserve yet?  Again, just return the
       // number
       if (this.reserve !== false && this.reserve.index >= 0 && index < this.reserve) {
         return number;
       }

       // Finally, let's say that we have a spot we'd like to reserve, and we
       // have reached the point where it has been inserted, as indicated by
       //
       if (this.reserve !== false && this.reserve.index >= 0 && index > this.reserve) {
         return this.parsedNumbers[index - 1];
       }

       return '';

     }

   }
}
</script>
