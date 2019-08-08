<!-- Renders a form that presents the user with a textarea for a message along
  with an emoji picker -->
<template>
  <form>

    <div class="shoutform-container">

      <textarea id="shoutMessage"
        placeholder="Enter the message you want to send"
        :value="value"
        @input="$emit('input', $event.target.value)"/>

      <a href="#" @click.prevent="picker = !picker"><i class="far fa-smile"></i></a>

    </div>

    <picker class="float-right emoji-picker" native="true" v-show="picker"
      @select="addEmoji"/>

  </form>
</template>

<script>
import { Picker } from 'emoji-mart-vue';
export default {
  name: "ShoutForm",
  props: ['value'],
  components: { Picker },

  data: function() {
    return {
      picker: false
    }
  },

  methods: {

    addEmoji: function(emoji) {
      this.$emit('input', this.value + emoji.native);
    }

  }

}
</script>

<style lang="scss" scoped>
.shoutform-container, form{
  position: relative;

  textarea {
    resize: none;
  }

  i {
    position: absolute;
    bottom: 2.4rem;
    right: 0.8rem;
    font-size: 2rem;
  }
}

.emoji-picker {
  position: absolute;
  right: 0;
}
</style>
