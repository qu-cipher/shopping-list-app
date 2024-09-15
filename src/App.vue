<script setup>
import './assets/main.css';

import { ref } from 'vue';
import Confirm from './components/ConfirmDialog.vue';
import 'bootstrap-icons/font/bootstrap-icons.css';

const header = ref('Shopping List App');
const name = ref('');
const quantity = ref(1);
const priority = ref(0); // 0 = Low | 1 = High

const editMode = ref(false);

const cart = ref([
  // {id: 1, name: "Mavad", quantity: 89, priority: 0},
  // {id: 2, name: "Teriak", quantity: 100, priority: 1},
  // {id: 3, name: "Cigar", quantity: 15, priority: 0}
]);

const selectedItemId = ref(null);

function getPriorityBadge(priority) {
  return (priority == 0)
        ? '<span class="badge text-bg-primary"><i class="bi bi-arrow-down-left"></i> Low</span>'
        : '<span class="badge text-bg-danger"><i class="bi bi-arrow-up-right"></i> High</span>';
};

function switchMode(){
  editMode.value = !editMode.value;
}

function resetQuantity() {
  if (quantity.value <= 0){
    quantity.value = 1;
  }
}

function addItem(){
    if (!name.value || quantity.value <= 0) {
        alert("Please provide valid name and quantity.");
        return;
    }

    cart.value.push({
        id: cart.value.length + 1, 
        name: name.value, 
        quantity: quantity.value, 
        priority: priority.value
    });

    name.value = "";
    quantity.value = 0;
    priority.value = 0; 
}

function confirmDelete(id) {
    selectedItemId.value = id;
}

function removeItem() {
    if (selectedItemId.value !== null) {
        cart.value = cart.value.filter(item => item.id !== selectedItemId.value);
        selectedItemId.value = null;
    }
}
</script>

<template>
  <div class="head">
    <h1>{{ header }}</h1>
    <div>
      <button class="btn btn-secondary" v-if="editMode" @click="switchMode">Cancel</button>
      <button class="btn btn-primary" v-else @click="switchMode">Alter</button>
    </div>
  </div>
  <br>
  <div class="container" v-if="editMode">
    <form class="form" v-on:submit.prevent="addItem">
        <div class="inps">
            <input 
                type="number" 
                placeholder="Quantity" 
                v-model.number="quantity" 
                class="number-inp"
                v-on:keyup.enter="addItem"
                @click="resetQuantity"/>
            <input 
                type="text" 
                placeholder="Name" 
                v-model="name"
                v-on:keyup.enter="addItem"/>
            <p :style="name.length < 4 || name.length > 100 ? 'color:rgb(255,0,0);' : 'color:rgb(0,255,0);'">{{ name.length }} / 100</p>
        </div>
        <div class="pro-con">
            <label for="priority">
                <h4 style="font-weight: lighter;">Priority:&nbsp;</h4>
            </label>
            <select 
                name="priority" 
                id="priority" 
                v-model.number="priority">
                <option value="0">Low</option>
                <option value="1">High</option>
            </select>
        </div>
      </form>
    
    <button class="btn btn-success" @click="addItem" :disabled="name.length < 4 || name.length > 100">Save Item</button>
  </div>
  
  <hr>
  
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>#</th>
          <th>Item Name</th>
          <th>Quantity</th>
          <th>Priority</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr 
            v-for="{ id, name, quantity, priority } in cart" 
            :key="id" 
            :class="((id%2)==0) ? 'second-row' : ''">
          <td>{{ id }}</td>
          <td>{{ name }}</td>
          <td>{{ quantity }}</td>
          <td style="text-align: center;" v-html="getPriorityBadge(priority)"></td>
          <td class="action">
              <button 
                  type="button" 
                  class="btn btn-outline-danger"            
                  @click="confirmDelete(id)" 
                  data-bs-toggle="modal" 
                  data-bs-target="#confirmModal"> 
                  <i class="bi bi-trash"></i>
                  Delete
              </button>
          </td>
        </tr>
      </tbody>
    </table>

    <p 
        style="padding-top: 25px; 
        color: rgba(255, 255, 255, 0.5); 
        text-align: center;" 
        v-if="!cart.length">
        NONE
    </p>
  </div>

  <Confirm @confirm="removeItem" />
</template>
