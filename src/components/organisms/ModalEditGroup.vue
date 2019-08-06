<!-- Modal content for editing a pre-existing group -->
<template>

  <div id="edit-group-modal-content" class="modal-content">

    <form @submit.prevent="editGroup">

      <h5>Edit Group </h5>

      <input type="text" placeholder="Name of group" v-model="name"/>

      <div class="row">

        <div class="column">

          <NumberField
            @numbers="numbers.raw = $event"/>

          <NumberList :numbers="parsedNumbers" />

        </div>

        <div class="column">

          <button class="button button-clear float-right" @click="$emit('transfer', {group, numbers: numbers.group})">
            <i class="fas fa-arrow-right"></i> Transfer Numbers
          </button>

        </div>

      </div>

      <div class="row">

        <div class="column">

          <span>
            {{ parsedNumbers.length }} numbers in total
          </span>

          <button type="submit" class="button button-clear float-right">
            <i class="fas fa-save"></i> Save Changes
          </button>

          <button class="button button-clear float-right float-right" @click="deleteGroup">
            <i class="fa fa-trash"></i> Delete Group
          </button>

          <button class="button button-clear float-right" @click="$emit('cancel')">
            <i class="fa fa-times"></i> Cancel
          </button>

        </div>
      </div>

    </form>

  </div>

</template>

<script>
import axios from 'axios';
import NumberField from '../molecules/NumberField.vue';
import NumberList from '../molecules/NumberList.vue';
import { parsePhoneNumberFromString } from 'libphonenumber-js';
export default {
  name: "ModalEditGroup",
  props: ['group'],
  components: { NumberField, NumberList },

  data: function() {
    return {
      name: this.group.name,
      numbers: {
        raw: [],
        group: []
      }
    }
  },

  computed: {

    // The numbers we get from the group are in a form where they're technically
    // objects, and also condensed into a set format.  Let's convert them into
    // simple strings with spaces thrown in, an also attach any user-added
    // numbers to the end
    parsedNumbers: function() {

      let numbers = [];
      this.numbers.group.forEach((number) => {

        let parsedNumber = parsePhoneNumberFromString(number.number, 'US');
        if (parsedNumber) {
          numbers.push(parsedNumber.formatInternational());
        }

      });

      numbers = numbers.concat(this.numbers.raw);

      return numbers;

    }

  },

  methods: {

    // Self explanatory.  It deletes the group
    deleteGroup: function() {

      axios.delete(`http://localhost:8088/groups/${this.group.id}`)
      .then((response) => {
        // TODO:
      })
      .catch((error) => {

      });

      this.$emit('removeGroup', this.group);

    },

    editGroup: function() {

      // If the name changed, then go ahead and update that

      // And update the numbers while you're at it.
      let groupID = this.group.id;
      this.numbers.raw.forEach((number) => {

        let condensedNumber = parsePhoneNumberFromString(number).number;

        let payload = JSON.stringify({
          data: {
            type: "phones",
            attributes: {
              number: condensedNumber
            }
          }
        });

        axios.post(`http://localhost:8088/groups/${groupID}/phones`, payload)
        .then((response) => {

        })
        .catch((error) => {

        });

      });

    }
  },

  // We need to get the numbers for this group once we mount the modal
  mounted: function() {

    axios.get(`http://localhost:8088/groups/${this.group.id}`)
    .then((response) => {

      let numbers = response.data.included;
      numbers.forEach((number) => {
        this.numbers.group.push({
          id: number.id,
          number: number.attributes.number
        });
      })

    })
    .catch((error) => {

    });

  }

}
</script>
