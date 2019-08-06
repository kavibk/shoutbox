<!-- THE modal, since the modal only really needs to do one thing.  Namely,
  provide a form for adding groups and a means to edit them. -->
<template>
  <div id="add-group-modal" aria-hidden="true" class="modal micromodal-slide">

    <div tabindex="-1" data-micromodal-close class="modal-overlay">

      <div role="dialog" aria-modal="true" aria-labelledby="group-modal-title"
        class="modal-container">

        <header class="modal-header">
          <p id="group-modal-title" class="modal-title">

            <strong v-if="groups.length">Add Group(s)</strong>
            <strong v-else>Add New Group</strong>

          </p>

          <button aria-label="Close modal" class="button button-clear" data-micromodal-close>
            <strong>
              <i class="fas fa-times-circle"></i>
              Close
            </strong>
          </button>
        </header>

        <div id="add-group-modal-content" class="modal-content">

          <!-- Modal content.  The edit group options appear first, but are hidden
            by default. -->
          <ModalEditGroup v-if="edit"
            :group="edit"
            @cancel="edit = false"
            @remove-group="removeGroup($event)"
            @transfer="startTransfer($event)"/>

          <ModalTransferNumbers v-if="transfer"
            :from-group="transfer"
            :groups="groups"/>

          <!-- Otherwise, we usually just have a list of pre-existing groups the
            user can add, if they exist of course. -->
          <ModalGroupList v-if="groups.length"
            :adding="addNew"
            :groups="groups"
            :transfer="transfer"
            @add-group="addNew = true"
            @confirm="confirmGroups($event)"
            @select="$emit('select', $event)"
            @edit="edit = $event"/>

          <!-- Finally, here's the part for adding a new group
          -->
          <ModalAddNewGroup v-if="addNew || groups.length == 0"
            :title="groups.length"
            @close="closeModal"/>

        </div>

      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import MicroModal from 'micromodal';
import ModalAddNewGroup from './ModalAddNewGroup.vue';
import ModalEditGroup from './ModalEditGroup.vue';
import ModalGroupList from './ModalGroupList.vue';
import ModalTransferNumbers from './ModalTransferNumbers.vue';
export default {
  name: "Modal",
  components: { ModalAddNewGroup, ModalEditGroup, ModalGroupList, ModalTransferNumbers },

  data: function() {
    return {
      addNew: false,
      edit: false,
      groups: [],
      transfer: false,
    }
  },

  methods: {

    closeModal: function() {
      document.getElementById('add-group-modal').classList.remove('is-open');
    },

    confirmGroups: function(groups) {
      this.$emit('groups', groups);
      document.getElementById('add-group-modal').classList.remove('is-open');
    },

    removeGroup: function(group) {

      this.edit = false;

      for(let i = 0; i < this.groups.length; i ++) {

        if (groups[i].id == group.id) {
          this.groups.slice(i, 1);
          return;
        }

      }

    },

    // Sets up the transfer numbers section of the modal
    startTransfer: function(payload) {

      this.edit = false;
      this.transfer = {
        group: payload.group,
        numbers: payload.numbers
      };

    }

  },

  mounted: function() {

    // Initialize modal
    MicroModal.init();

    // And go ahead and fetch groups
    let groups = this.groups;
    axios.get(`http://localhost:8088/groups`)
    .then((response) => {

      response.data.data.forEach((group) => {
        groups.push({
          id: group.id,
          name: group.attributes.name
        });
      });

    })
    .catch((error) => {
      // TODO
    });

  }
}
</script>

<style lang="scss">
// Right then, so this is all copy-pasted from here https://gist.github.com/ghosh/4f94cf497d7090359a5c9f81caf60699
// Micromodal is a helpful minimal little modal library / package, but the
// author opted to include absolutely no styling whatsoever.

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-container {
  background-color: #fff;
  padding: 30px;
  width: 60%;
  max-height: 100vh;
  border-radius: 4px;
  overflow-y: auto;
  box-sizing: border-box;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  * {
    margin-top: 0;
    margin-bottom: 0;
  }
}

.modal-close {
  background: transparent;
  border: 0;
}

.modal-content {
  margin-top: 2rem;
  margin-bottom: 2rem;
}
/**************************\
  Demo Animation Style
\**************************/
@keyframes mmfadeIn {
    from { opacity: 0; }
      to { opacity: 1; }
}

@keyframes mmfadeOut {
    from { opacity: 1; }
      to { opacity: 0; }
}

@keyframes mmslideIn {
  from { transform: translateY(15%); }
    to { transform: translateY(0); }
}

@keyframes mmslideOut {
    from { transform: translateY(0); }
    to { transform: translateY(-10%); }
}

.micromodal-slide {
  display: none;
}

.micromodal-slide.is-open {
  display: block;
}

.micromodal-slide[aria-hidden="false"] .modal-overlay {
  animation: mmfadeIn .3s cubic-bezier(0.0, 0.0, 0.2, 1);
}

.micromodal-slide[aria-hidden="false"] .modal-container {
  animation: mmslideIn .3s cubic-bezier(0, 0, .2, 1);
}

.micromodal-slide[aria-hidden="true"] .modal-overlay {
  animation: mmfadeOut .3s cubic-bezier(0.0, 0.0, 0.2, 1);
}

.micromodal-slide[aria-hidden="true"] .modal-container {
  animation: mmslideOut .3s cubic-bezier(0, 0, .2, 1);
}

.micromodal-slide .modal-container,
.micromodal-slide .modal-overlay {
  will-change: transform;
}
</style>
