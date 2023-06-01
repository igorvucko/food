
<template>
  <div>
    <h1 class="foodica text-5xl font-black"></h1>

    <v-data-table
      :headers="headers"
      :items="fridgeA"
      class="elevation-1"
      :footer-props="{
        'items-per-page-options': [10, 20, 30],
        'show-current-page': true,
        'items-per-page-text': 'Items per page'
      }"
    >

      <template #item.name="{ item }">
        <div class="text-center">
          {{ item.name }}
        </div>
      </template>
      <template #item.cost="{ item }">
        <div class="text-center">
          {{ item.cost }}
        </div>
      </template>
      <template #item.expirationDate="{ item }">
        <div class="text-center">
          {{ item.expirationDate }}
        </div>
      </template>
      <template #item.quantity="{ item }">
        <div class="text-center">
          {{ item.quantity }}
        </div>
      </template>
      <!-- eslint-disable vue/valid-v-slot -->
      <template #item.action="{ item }">
        <v-icon small class = "mr-1" @click="startUpdate(item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="removeItem(item.name)">
          mdi-delete
        </v-icon>
      </template>
    </v-data-table>

    <div v-if="updateItem">
      <input v-model="updateItem.name" placeholder="Item name">
      <input v-model="updateItem.cost" placeholder="Item cost">
      <input v-model="updateItem.expirationDate" placeholder="Expiration Date">
      <input v-model="updateItem.quantity" placeholder="Quantity">
      <v-btn color="primary" dark @click="commitUpdate">
        Update Item
      </v-btn>
      <v-btn color="red" dark @click="cancelUpdate">
        Cancel
      </v-btn>
    </div>

    <div v-else class="action-container">
      <div>
        <input v-model="newItem.name" placeholder="Item name">
        <input v-model="newItem.expirationDate" placeholder="Expiration Date">
        <input v-model="newItem.quantity" placeholder="Quantity">
        <input v-model="newItem.cost" placeholder="Item cost">
        <v-btn color="primary" dark @click="addItem">
          Add Item
        </v-btn>
        <span class="total-cost pl-36">
        <span>Total Cost: {{ calculateTotalCost }}</span>
        </span>
      </div>
       </div>
  </div>
</template>

<script>
import { mapState, mapMutations, mapActions } from 'vuex'

export default {
  data () {
    return {
      newItem: {
        name: '',
        cost: '',
        expirationDate: '',
        quantity: ''
      },
      updateItem: null,
      oldName: null,
      headers: [
        { text: 'Name', value: 'name' },
        { text: 'Expiration Date', value: 'expirationDate' },
        { text: 'Quantity', value: 'quantity' },
        { text: 'Cost', value: 'cost' },
        { text: 'Actions', value: 'action', sortable: false }
      ]
    }
  },
  computed: {
    ...mapState(['fridgeA']),
    calculateTotalCost () {
      return this.fridgeA.reduce((total, item) => total + parseFloat(item.cost || 0), 0)
    }
  },
  async mounted () {
    await this.$store.dispatch('loadLocalStorage')
  },
  beforeDestroy () {
    this.$store.dispatch('saveToLocalStorage')
  },
  methods: {
    ...mapMutations(['addToFridge', 'removeFromFridge', 'updateFridgeItem']),
    ...mapActions(['saveToLocalStorage']),

    startUpdate (item) {
      this.updateItem = { ...item }
      this.oldName = item.name
    },
    commitUpdate () {
      const updatedItem = {
        oldName: this.oldName,
        newItem: {
          name: this.updateItem.name,
          cost: this.updateItem.cost,
          expirationDate: this.updateItem.expirationDate,
          quantity: this.updateItem.quantity
        }
      }
      this.updateFridgeItem(updatedItem)
      this.updateItem = null
      this.oldName = null
      this.saveToLocalStorage()
    },
    cancelUpdate () {
      this.updateItem = null
      this.oldName = null
    },
    removeItem (itemName) {
      this.removeFromFridge(itemName)
      this.saveToLocalStorage()
    },
    addItem () {
      const newItem = {
        name: this.newItem.name,
        cost: this.newItem.cost,
        expirationDate: this.newItem.expirationDate,
        quantity: this.newItem.quantity
      }
      this.addToFridge(newItem)
      this.newItem.name = ''
      this.newItem.cost = ''
      this.newItem.expirationDate = ''
      this.newItem.quantity = ''
      this.saveToLocalStorage()
    }
  }
}
</script>