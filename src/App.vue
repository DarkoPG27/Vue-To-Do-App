<script setup>
import { ref, computed } from "vue";
import useLocalStorage from "./composables/useLocalStorage";

const header = ref("Vue To-Do List");

const items = useLocalStorage(
  [
    { id: 1, label: "Ride 100km bike", purchased: true, highPriority: false },
    { id: 2, label: "Learn The Vue", purchased: true, highPriority: true },
    { id: 3, label: "Enjoj the life", purchased: false, highPriority: true },
  ],
  "listOfDoDoItems"
);

const reversedItems = computed(() => [...items.value].reverse());

const showOnlyActiveDoTo = computed(() =>
  [...reversedItems.value].filter((item) => item.purchased !== true)
);
const newItem = ref("");

const newItemHighPriority = ref(false);

const toDo = ref(false);

const saveItem = () => {
  items.value.push({
    id: items.value.length + 1,
    label: newItem.value,
    highPriority: newItemHighPriority.value,
  });
  newItem.value = "";
  newItemHighPriority.value = false;
};

const deleteItem = (itemToDelete) => {
  items.value = [...items.value].filter((item) => item.id !== itemToDelete.id);
};

const togglePurchased = (item) => {
  item.purchased = !item.purchased;
};
</script>

<template>
  <div class="container">
    <div class="header">
      <h1>{{ header }}</h1>
    </div>

    <form @submit.prevent="saveItem">
      <div class="add_item_form">
        <input
          v-model="newItem"
          type="text"
          placeholder="Add todo"
          class="input_todo"
        />

        <div class="list_options">
          <div class="labels">
            <Label>
              <input
                :disabled="newItem.length < 5"
                type="checkbox"
                v-model="newItemHighPriority"
              />
              HighPriority
            </Label>
            <Label>
              <input type="checkbox" v-model="toDo" />
              Only To Do
            </Label>
          </div>
          <div class="button_div">
            <button :disabled="newItem.length < 5" class="add_item_btn">
              Add Item
            </button>
          </div>
        </div>
      </div>
    </form>

    <ul v-show="!toDo" class="scrollable_list">
      <li v-for="item in reversedItems" :key="item.id" class="item_from_list">
        <span
          class="item_text"
          :class="{ strikeout: item.purchased, priority: item.highPriority }"
          @click="togglePurchased(item)"
          >{{ item.label }}</span
        >
        <button
          :disabled="!item.purchased"
          class="delete_btn"
          @click="deleteItem(item)"
        >
          Delete
        </button>
      </li>
    </ul>
    <ul v-show="toDo" class="scrollable_list">
      <li
        v-for="item in showOnlyActiveDoTo"
        :key="item.id"
        class="item_from_list"
      >
        <span
          :class="{ strikeout: item.purchased, priority: item.highPriority }"
          @click="togglePurchased(item)"
          >{{ item.label }}</span
        >
      </li>
    </ul>
    <div v-show="!toDo">
      <p v-if="!items.length">Nothing to se here :D</p>
    </div>
    <div v-show="toDo">
      <p v-if="!showOnlyActiveDoTo.length">Nothing to se here :D</p>
    </div>
    <hr />
    <div class="footer">
      <div>Created By DB27.</div>
      <div>&#169;2024 All Rights Reserved.</div>
    </div>
  </div>
</template>

<style scoped></style>
