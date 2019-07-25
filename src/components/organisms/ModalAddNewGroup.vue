<!-- Renders portion of modal that handles creating a new group with new
  numbers and the like -->
<template>

  <div>

    <header class="modal-header">
      <p id="group-modal-title" class="modal-title">
        <strong>Add New Group</strong>
      </p>

      <button aria-label="Close modal" class="button button-clear" data-micromodal-close>
        <strong>
          <i class="fas fa-times-circle"></i>
          Close
        </strong>
      </button>
    </header>

    <div id="add-group-modal-content" class="modal-content">

      <form @submit.prevent="createGroup">

        <input type="text" placeholder="Name of group" v-model="name"/>

        <div class="row">

          <div class="column">

            <NumberField
              @numbers="numbers = $event"/>

            <NumberList :numbers="numbers" />

          </div>

          <div class="column">

            <button type="submit" class="button button-clear float-right" v-if="validGroup">
              Save Group
            </button>

          </div>

        </div>

      </form>
    </div>

  </div>

</template>

<script>
import NumberField from '../molecules/NumberField.vue';
import NumberList from '../molecules/NumberList.vue';
export default {
  name: "ModalAddNewGroup",
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

    // Used to create a group, naturally
    createGroup: function() {

      // Make sure name is present and we have some numbers
      if (this.name.length == 0 || this.numbers.length == 0) {
        return;
      }

      // Otherwise, make the request.
      let phoneIncludes = [];
      this.numbers.forEach((number) => {
        phoneIncludes.push({
          type: "phones",
          attributes: {
            number
          }
        })
      });

      let payload = JSON.stringify({
        data: {
          type: "groups",
          attributes: {
            name: this.name
          }
        },
        included: phoneIncludes
      });
      axios.post(`/groups`, payload)
      .then((response) => {
        // TODO
      })
      .catch((error) => {
        // TODO:
      });

    }

  }

}
</script>
