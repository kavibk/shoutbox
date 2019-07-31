<template>
  <div id="app">

    <Modal
      @groups="addGroups($event)"/>

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
          <button class="button button-clear" data-micromodal-trigger="add-group-modal">
            Add Group
          </button>
        </div>

      </div>

      <div class="row">

        <div class="column">
          <NumberList :numbers="numbers" />
        </div>

        <div class="column">

          <GroupList
            :groups="groups"
            :selected="groups"
            :selectable="false"
            :removeable="true"
            :editable="false"
            @un-select="removeGroup($event)"/>
            
        </div>

      </div>

    </div>
  </div>
</template>

<script>
import GroupList from './components/molecules/GroupList.vue';
import Modal from './components/organisms/Modal.vue';
import NumberField from './components/molecules/NumberField.vue';
import NumberList from './components/molecules/NumberList.vue';
import ShoutForm from './components/molecules/ShoutForm.vue';
export default {
  name: 'app',
  components: {
    GroupList,
    Modal,
    NumberField,
    NumberList,
    ShoutForm
  },

  data: function() {
    return {
      groups: [],
      numbers: []
    }
  },

  methods: {

    // Adds groups selected from the group selection modal to the array of
    // groups here.  Checks for duplicates and removes them
    addGroups: function(groups) {

      let _this = this;
      groups.forEach((group) => {

        for(let i = 0; i < _this.groups.length; i ++) {
          if (_this.groups[i].id == group.id) {
            return;
          }
        }

        _this.groups.push(group);

      });

    },

    removeGroup: function(group) {

      for(let i = 0; i < this.groups.length; i ++) {
        if (this.groups[i].id == group.id) {
          this.groups.splice(i, 1);
          return;
        }
      }

    }

  }

}
</script>

<style lang="scss" src="./styles/styles.scss"/>
