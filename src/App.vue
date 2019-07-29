<template>
  <div id="app">

    <Modal />

    <header class="brand">
      <img alt="KAVI Logo" src="./assets/KAVI_logo_black.png"/>
      <h1>Shoutbox</h1>
    </header>

    <div class="container">
      <ShoutForm />

      <div class="row">

        <div class="column">
          <NumberField
            @numbers="numbers = $event"/>
          <NumberList :numbers="numbers" />
        </div>

        <div class="column">
          <a href="#" data-micromodal-trigger="add-group-modal">Add Group</a>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import Modal from './components/organisms/Modal.vue';
import NumberField from './components/molecules/NumberField.vue';
import NumberList from './components/molecules/NumberList.vue';
import ShoutForm from './components/molecules/ShoutForm.vue';
export default {
  name: 'app',
  components: {
    Modal,
    NumberField,
    NumberList,
    ShoutForm
  },

  data: function() {
    return {
      numbers: []
    }
  }

}
</script>

<style lang="scss" src="./styles/styles.scss"/>
