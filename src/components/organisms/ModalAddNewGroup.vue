<!-- Renders portion of modal that handles creating a new group with new
  numbers and the like -->
<template>

  <form @submit.prevent="createGroup">

    <h5 v-if="title">Add New Group</h5>

    <input type="text" placeholder="Name of group" v-model="name"/>

    <div class="row">

      <div class="column">

        <NumberField
          @numbers="addNumbers($event)"/>

        <NumberList
          :numbers="numbers"
          :reserve="false"/>

      </div>

      <div class="column">

        <div class="float-right">

          <button type="button" class="button button-clear"
            :class="{'padding-0-right': !validGroup}"
            @click="$emit('cancel')">
            <i class="fa fa-times"></i> Cancel
          </button>

          <button type="submit" class="button button-clear padding-0-right" v-if="validGroup">
            <i class="fa fa-save"></i> Save Group
          </button>

        </div>

      </div>

    </div>

  </form>

</template>

<script>
import axios from 'axios';
import NumberField from '../molecules/NumberField.vue';
import NumberList from '../molecules/NumberList.vue';
import { parsePhoneNumberFromString } from 'libphonenumber-js';
export default {
  name: "ModalAddNewGroup",
  props: ['title'],
  components: { NumberField, NumberList },

  data: function() {
    return {
      name: '',
      numbers: []
    }
  },

  computed: {

    // Used to determine if we have all we need to create a valid group.  From
    // the backend's perspective, we really only need a name, but the frontend
    // goes further and wants numbers too
    validGroup: function() {
      if (this.name.length && this.numbers.length) {
        return true;
      }
      return false;
    }

  },

  methods: {

    addNumbers: function(numbers) {

      numbers.forEach((number) => {
        this.numbers.push(number);
      });

    },

    // Used to create a group, naturally
    createGroup: function() {

      // Make sure name is present and we have some numbers
      if (this.name.length == 0 || this.numbers.length == 0) {
        return;
      }

      // Otherwise, make the request.
      let data = {
        data: {
          type: "groups",
          attributes:
          {
            name: this.name
          },
          relationships: {
            phones: {
              data: []
            }
          },
        },
        included: []
      }

      let phoneIncludes = [];
      this.numbers.forEach((number) => {

        let condensedNumber = parsePhoneNumberFromString(number).number;

        data.data.relationships.phones.data.push({
          id: condensedNumber,
          type: "phones"
        });

        data.included.push({
          id: condensedNumber,
          type: "phones",
          attributes: {
            number: condensedNumber
          }
        });

      });

      let payload = JSON.stringify(data);
      axios.post(`http://localhost:8088/groups`, payload)
      .then((response) => {
        this.$emit('done', {
          id: response.data.data.id,
          name: response.data.data.attributes.name
        });
      })
      .catch((error) => {
        // TODO:
      });

    }

  }

}
</script>
