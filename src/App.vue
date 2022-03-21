<template>
  <div>
    <!-- Html -->
    <h1>Items List</h1>
    <div :class="$style.appContent">
      <SearchBar
        v-model:searchQuery="searchQuery"
        :queryResult="queryResult"
        @data-added="addItem"
      />
      <br />
      <ListItem
        :search="searchQuery"
        :listItems="items"
        @found-items="setSearchResult($event)"
        @remove="removeItem($event)"
      />
    </div>
    <!-- <div>
      <ListSorter />
    </div> -->
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import _ from 'lodash';
import SearchBar from './components/SearchBar.vue';
import ListItem from './components/ListItem.vue';
// import ListSorter from './components/ListSorter.vue';
import Item from './types/Item';

export default defineComponent({
  name: 'App',
  components: {
    SearchBar,
    ListItem,
    // ListSorter,
  },
  setup() {
    // Setup
    const items = ref<Item[]>([]);
    const getItems = () => {
      let list = JSON.parse(window.localStorage.getItem('items') || '[]');
      items.value = list;
    };
    const queryResult = ref(items.value.length);

    const setSearchResult = (event: number) => {
      queryResult.value = event;
    };

    const removeItem = (event: Item) => {
      _.remove(items.value, function (obj) {
        return obj.id === event.id;
      });
      window.localStorage.setItem('items', JSON.stringify(items.value));
    };

    getItems();

    const lastItem = computed<Item>(() => {
      return items.value[items.value.length - 1];
    });

    return {
      items,
      getItems,
      setSearchResult,
      queryResult,
      lastItem,
      removeItem,
    };
  },

  data() {
    return {
      searchQuery: '',
    };
  },
  methods: {
    addItem() {
      let date = new Date();
      let item = {
        id: this.lastItem ? this.lastItem.id + 1 : 1,
        value: this.searchQuery,
        addedDate: date.toISOString(),
      };
      this.items.push(item);
      window.localStorage.setItem('items', JSON.stringify(this.items));
      this.getItems();
      this.searchQuery = '';
    },
  },
});
</script>

<style lang="scss" module>
// Style
.appContent {
  padding: 0;
}
</style>
