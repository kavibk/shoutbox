<!-- A template for rendering a list of numbers.  Accepts as a prop an array
  containing strings that have some kind of valid phone number. -->
<template>
  <ul>
    <NumberListItem v-for="number in parsedNumbers"
      :number="number"/>
  </ul>
</template>

<script>
import { parsePhoneNumberFromString } from 'libphonenumber-js';
import NumberListItem from '../atoms/NumberListItem.vue';
export default {
  name: "NumberList",
  props: ['numbers'],
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

    }

  }
}
</script>
