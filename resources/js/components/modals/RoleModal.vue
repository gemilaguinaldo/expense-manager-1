<script>
  export default {
    props: ['state', 'title', 'role'],
    name: 'modal',
    data: function () {
      return {
        roleData: this.role,
        error: [],
        hasError: false
      }
    },
    methods: {
      close() {
        this.error = []
        this.hasError = false
        this.$emit('close');
      },
      save() {
        let params = this.roleData;
        let currentObj = this;
        if (params.id) {
          this.axios.put(`/role/${params.id}`, params)
          .then(function (response) {
              if (response.data.error) {
                currentObj.error = response.data.error;
                currentObj.hasError = true
              } else {
                currentObj.output = response.data;
                currentObj.close()
                currentObj.hasError = false
              }
          })
          .catch(function (error) {
              currentObj.hasError = true
              currentObj.error = {'message': [error]};
          });
        } else {
          this.axios.post(`/role`, params)
          .then(function (response) {
              if (response.data.error) {
                currentObj.error = response.data.error;
                currentObj.hasError = true
              } else {
                currentObj.output = response.data;
                currentObj.close()
                currentObj.hasError = false
              }
          })
          .catch(function (error) {
              currentObj.hasError = true
              currentObj.error = {'message': [error]};
          });
        }
      },
      deleteRole() {
        let params = this.roleData;
        let currentObj = this;
        this.axios.delete(`/role/${params.id}`)
          .then(function (response) {
              if (response.data.error) {
                currentObj.error = response.data.error;
                currentObj.hasError = true
              } else {
                currentObj.output = response.data;
                currentObj.close()
                currentObj.hasError = false
              }
          })
          .catch(function (error) {
              currentObj.hasError = true
              currentObj.error = {'message': [error]};
          });
      }
    },
  };
</script>
<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">

          <div class="modal-header">
            <slot name="header">
              <h3 class="h3">{{ state | capitalize }} {{ title | capitalize }}</h3>
            </slot>
          </div>

          <div class="modal-body">
            <slot name="body">
              <div v-if="hasError" class="alert alert-danger" role="alert">
                <div v-for="err in error">
                  <span v-for="e in err">{{ e }}</span>
                </div>
              </div>
              <form autocomplete="off" v-on:submit.prevent="save" method="post">
                <div class="form-group">
                  <input v-if="roleData.id" type="hidden" id="id" name="custId" v-model="roleData.id">
                </div>
                <div class="form-group">
                    <label for="name">Display Name</label>
                    <input type="text" id="name" class="form-control" placeholder="Sam Carter" v-model="roleData.name">
                </div>
                <div class="form-group">
                    <label for="description">Description</label>
                    <input type="description" id="description" class="form-control" placeholder="role description" v-model="roleData.description">
                </div>
                <div class="float-left pull-left" v-if="roleData.id">
                  <div class="btn btn-danger" @click="deleteRole">delete</div>
                </div>
                <div class="float-right pull-right">
                  <div class="btn btn-info" @click="close">cancel</div>
                  <button type="submit" class="btn btn-success">save</button>
                </div>
              </form>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<style type="text/css">
  .modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 600px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
  transition: all .3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>