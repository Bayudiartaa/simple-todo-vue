<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <table class="table table-bordered mt-5">
          <thead>
            <tr>
              <th>Todo</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item,i) in todoList" :key="i">
              <td class="align-middle w-75">
                {{ item.task }}
              </td>
              <td class="align-middle text-center w-75">
                <button class="btn btn-info btn-sm mx-1" 
                    v-on:click="handleEdit(item.id)">Edit
                </button>
                <button class="btn btn-danger btn-sm mx-1" 
                    @click="handleDelete(item.id)">Delete
                </button>
              </td>
            </tr>
          </tbody>
          <tfoot>
                <td class="align-middle text-center w-75">
                  <div class="form-group">
                    <label for="">{{
                        editMode ? 'Edit' : 'Add'
                      }}</label>
                      <input type="text" class="form-control" 
                      v-model="todoItem.task"/>
                  </div>
                </td>
                <td class="align-middle text-center w-75">
                  <button class="btn btn-primary btn-sm mx-1" 
                    v-on:click="handleTodoItem">
                      {{
                        editMode ? 'Edit' : 'Add'
                      }}
                  </button>
                  <button 
                    v-if="editMode"
                    class="btn btn-danger btn-sm mx-1" 
                    v-on:click="handleCancel">Cancel
                    </button>
                </td>
              </tfoot> 
        </table>
      </div>
    </div>
  </div>
</template>

<script>

import Axios from "axios";
let baseUrl = "http://localhost:3500/todo"

export default {
  data() {
    return {
      todoList : [],
      todoItem: {},
      editMode: false
    }
  },
  methods : {
    handleEdit(id) {
      this.editMode = true;
      this.todoItem = this.todoList.find(item => item.id === id);
    },
    handleCancel() {
      this.editMode = false;
      this.todoItem = "";
    },
    async handleTodoItem() {
      const id = this.todoItem.id;

        if(this.editMode) {
          await Axios.put(`$(baseUrl)/${id}`, this.todoItem);
          this.editMode = false;
          this.todoItem = "";
        } else {
          await Axios.post(baseUrl, this.todoItem);
          this.todoItem.task = "";
        }
        Axios.get(baseUrl).then(
            (response) => (this.todoList = response.data)
        );
    },
    async handleDelete(id) {
        await Axios.delete(`http://localhost:3500/todo/${id}`);
        Axios.get(baseUrl).then(
            (response) => (this.todoList = response.data)
        );
    },
  },
  created() {
    Axios.get(baseUrl).then((response) => (this.todoList = response.data));
  },
};
</script>
