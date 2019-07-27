<!-- Modal content for editing a pre-existing group -->
<template>

  <div id="edit-group-modal-content" class="modal-content">

    <form @submit.prevent="editGroup">

      <input type="text" placeholder="Name of group" v-model="name"/>

      <div class="row">

        <div class="column">

          <NumberField
            @numbers="numbers = $event"/>

          <NumberList :numbers="numbers" />

        </div>

        <div class="column">

          <button class="button button-clear float-right">
            Transfer Numbers
          </button>

        </div>

      </div>

      <div class="row">

        <div class="column">
          (3) (1 Duplicate)
        </div>

        <div class="column">

          <button type="submit" class="button button-clear float-right">
            Save Changes
          </button>

          <button class="button button-clear float-right">
            Delete Group
          </button>

        </div>
      </div>

    </form>
    
  </div>

</template>

<script>
import NumberField from '../molecules/NumberField.vue';
import NumberList from '../molecules/NumberList.vue';
export default {
  name: "ModalEditGroup",
  components: { NumberField, NumberList }
}
</script>
