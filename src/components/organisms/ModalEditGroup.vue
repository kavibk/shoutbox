<!-- Modal content for editing a pre-existing group -->
<template>

  <div>

    <header class="modal-header">
      <p id="group-modal-title" class="modal-title">
        <strong>Edit Group</strong>
      </p>

      <button aria-label="Close modal" class="button button-clear" data-micromodal-close>
        <strong>
          <i class="fas fa-times-circle"></i>
          Close
        </strong>
      </button>
    </header>

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
  name: "ModalEditGroup",
  components: { NumberField, NumberList }
}
</script>
