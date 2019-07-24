<!-- THE modal, since the modal only really needs to do one thing.  Namely,
  provide a form for adding groups and a means to edit them. -->
<template>
  <div id="add-group-modal" aria-hidden="true" class="modal micromodal-slide">

    <div tabindex="-1" data-micromodal-close class="modal-overlay">

      <div role="dialog" aria-modal="true" aria-labelledby="add-group-modal-title"
        class="modal-container">

        <header class="modal-header">
          <p id="add-group-modal-title" class="modal-title">
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
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import MicroModal from 'micromodal';
import NumberField from './NumberField.vue';
import NumberList from './NumberList.vue';
export default {
  name: "Modal",
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

  },

  mounted: function() {
    MicroModal.init();
  }
}
</script>

<style lang="scss" scoped>
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
  width: 80%;
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
