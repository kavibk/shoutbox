<!-- Component for capturing numbers that the user wants to send a message to.
  The field presents itself as a single-line text input that can accept one
  number or many numbers.  The field is joined by a button that, when pressed,
  bubbles up numbers as an event. -->
<template>
  <form @submit.prevent="addNumber">
    <input type="text" placeholder="Enter Number(s)"
      :value="value"
      @input="numberWhileTyping"
      @paste.prevent="parseNumbers"
      />
    <button class="button" type="submit">
      Add Number
    </button>
  </form>
</template>

<script>
import { AsYouType, findNumbers } from 'libphonenumber-js';
export default {
  name: "NumberField",
  data: function() {
    return {
      value: ''
    }
  },

  methods: {

    // Bubbles up a number from the input to parent component, but only if
    // said number is a valid number
    addNumber: function() {

    },

    // Handles input events.  Analyzes text as the user is typing and attempts
    // to convert into a valid number
    numberWhileTyping: function(event) {

      let raw = event.target.value;
      this.value = new AsYouType('US').input(raw);

    },


    // Takes input event from our input element and attempts to parse numbers
    // out of it.
    parseNumbers: function(event) {

      let text = (event.clipboardData || window.clipboardData).getData('text');
      let numberObjects = findNumbers(
        text,
        'US',
        { v2: true }
      );

      // As the name implies, numberObjects contains objects containing the
      // phone number, among other things.  We just want the numbers as strings
      let numbers = [];
      numberObjects.forEach((number) => {
        numbers.push(number.number.number); // Yes, really
      })
      this.$emit('numbers', numbers);

    }

  }
}
</script>
