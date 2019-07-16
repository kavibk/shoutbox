<!-- Renders a form that presents the user with a textarea for a message along
  with an emoji picker -->
<template>
  <form>
    <div class="shoutform-container">
      <textarea id="shoutMessage" placeholder="Enter the message you want to send"
        v-model="message"/>
      <a href="#" @click.prevent="picker = !picker"><i class="far fa-smile"></i></a>
    </div>
    <picker class="float-right" native="true" v-show="picker"/>
  </form>
</template>

<script>
import { Picker } from 'emoji-mart-vue';
export default {
  name: "ShoutForm",
  components: {
    Picker
  },

  data: function() {
    return {
      picker: true
    }
  }

}
</script>

<style lang="scss" scoped>
.shoutform-container {
  position: relative;

  textarea {
    resize: none;
  }

  i {
    position: absolute;
    bottom: 2.4rem;
    right: 0.8rem;
  }
}
</style>
