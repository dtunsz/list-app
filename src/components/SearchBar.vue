<template>
  <div :class="$style.search">
    <div :class="$style.inputClass">
      <input
        :value="searchQuery"
        type="text"
        placeholder="Search or Add"
        :class="$style.input"
        @input="$emit('update:searchQuery', $event.target.value)"
        @keyup.enter="addNewItem"
        @keyup.esc="removeSearchQuery"
      />
      <div v-if="searchQuery" :class="$style.iconContainer">
        <CancelIcon
          :class="$style.icon"
          @click="$emit('update:searchQuery', '')"
        />
        <AddIcon
          v-if="queryResult === 0"
          :class="[$style.enabledAdd, $style.icon]"
          @click="$emit('data-added')"
        />
        <AddIcon v-else :class="[$style.disabledAdd, $style.icon]" />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import CancelIcon from './icons/CancelIcon.vue';
import AddIcon from './icons/AddIcon.vue';

export default defineComponent({
  name: 'SearchBar',
  components: {
    CancelIcon,
    AddIcon,
  },
  props: {
    searchQuery: {
      type: String,
      required: true,
    },
    queryResult: {
      type: Number,
      required: true,
    },
  },

  setup(props, { emit }) {
    const addNewItem = () => {
      if (props.queryResult === 0) {
        emit('data-added');
      }
      return;
    };
    const removeSearchQuery = () => {
      emit('update:searchQuery', '');
    };
    return {
      addNewItem,
      removeSearchQuery,
    };
  },
});
</script>

<style lang="scss" module>
@use 'sass/color';

.search {
  width: 77%;
}
.inputClass {
  position: relative;
  display: inline;
}
.iconContainer {
  position: relative;
  float: right;
  margin-top: -50px;

  .icon {
    margin: 0px 14px;
  }
}

.input {
  padding: 20px;
  width: 100%;
  margin-bottom: 10px;
  background: color.$off-white;
  border: none;
  border-radius: 6px;
}

.enabledAdd {
  color: color.$icon-sea-green;
  cursor: pointer;
}

.disabledAdd {
  color: color.$dull-gray;
  cursor: not-allowed;
}
</style>
