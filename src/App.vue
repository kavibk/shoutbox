<template>
  <div id="app">

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
        </div>

        <div class="column">
          <NumberList :numbers="numbers" />
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import NumberField from './components/molecules/NumberField.vue';
import NumberList from './components/molecules/NumberList.vue';
import ShoutForm from './components/molecules/ShoutForm.vue';
export default {
  name: 'app',
  components: {
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
