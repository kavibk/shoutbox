<template>
  <div id="app">

    <div>

      <Modal
        @groups="addGroups($event)"/>

      <header class="brand">
        <img alt="KAVI Logo" src="./assets/KAVI_logo_black.png"/>
        <h1>Shoutbox</h1>
      </header>

      <div class="container">

        <ShoutForm v-model="message"/>

        <div class="row">

          <div class="column">
            <NumberField @numbers="numbers = numbers.concat($event)"/>
          </div>

          <div class="column">
            <button class="button button-clear padding-0-left" data-micromodal-trigger="add-group-modal">
              Add Group
            </button>
          </div>

        </div>

        <div class="row">

          <div class="column">
            <NumberList
              :numbers="numbers"
              :removeable="true"
              @remove="removeNumber"/>
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

        <Loader class="center" v-if="sending" />

        <button class="button button-clear centered" v-else-if="sent" @click="reset">
          <i class="fa fa-check"/>
          Sent
          <i class="fa fa-refresh"/>
          Refresh?
        </button>

        <button class="button shout-button" v-else-if="message != '' && numbers.length || groups.length"
          @click="shout">
          Shout
        </button>

      </div>

    </div>

  </div>
</template>

<script>
import axios from 'axios';
import GroupList from './components/molecules/GroupList.vue';
import Loader from './components/atoms/Loader.vue';
import Modal from './components/organisms/Modal.vue';
import NumberField from './components/molecules/NumberField.vue';
import NumberList from './components/molecules/NumberList.vue';
import ShoutForm from './components/molecules/ShoutForm.vue';
export default {
  name: 'app',
  components: {
    GroupList,
    Loader,
    Modal,
    NumberField,
    NumberList,
    ShoutForm
  },

  data: function() {
    return {
      message: '',
      groups: [],
      numbers: [],
      sending: false,
      sent: false
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

    },

    removeNumber: function(number) {

      for(let i = 0; i < this.numbers.length; i ++) {
        if (this.numbers[i] == number) {
          this.numbers.splice(i, 1);
          return;
        }
      }
    },

    reset: function() {
      this.numbers = [];
      this.groups = [];
      this.message = '';
      this.sent = false;
    },

    shout: function() {

      this.sending = true;

      let payload = {
        data: {
          type: "shout",
          attributes: {
            body: this.message,
          },
          relationships: {
            phones: {
              data: []
            },
            groups: {
              data: []
            }
          }
        },
        included: []
      }

      this.groups.forEach((group) => {

        payload.data.relationships.groups.data.push({
          id: group.id,
          type: "groups"
        });

        payload.included.push({
          id: group.id,
          type: "groups",
          attributes: {
            name: group.name
          }
        });

      });

      this.numbers.forEach((number) => {

        payload.data.relationships.phones.data.push({
          id: "0",
          type: "phones"
        });

        payload.included.push({
          id: "0",
          type: "phones",
          attributes: {
            number
          }
        });

      });

      let data = JSON.stringify(payload);

      axios.post(`${process.env.VUE_APP_ENDPOINT}/shout`, data)
      .then((response) => {

        this.sending = false;
        this.sent = true;

      })
      .catch((error) => {
        // TODO
      });

    }

  }

}
</script>

<style lang="scss" src="./styles/styles.scss"/>
