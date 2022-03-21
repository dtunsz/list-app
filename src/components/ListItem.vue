<template>
  <div :class="$style.listView">
    <div :class="$style.main">
      <ul :class="$style.list">
        <li
          v-for="item in itemsFiltered"
          :key="item.id"
          :class="$style.listItem"
        >
          <span :class="$style.valueContainer">
            <CheckIcon
              v-if="lowerCase(search) === lowerCase(item.value)"
              :class="$style.check"
            />
            <div :class="$style['main-value']">
              {{ item.value }}
              <small
                ><span
                  v-if="lowerCase(search) === lowerCase(item.value)"
                  :class="$style.check"
                  >Exact match</span
                >
                {{ lowerCase(search) === lowerCase(item.value) ? ',' : '' }}
                #{{ item.id }}
              </small>
            </div>
          </span>
          <span>
            <span :class="$style.move">
              {{ formatDate(item.addedDate) }}
            </span>
            <TrashIcon
              :class="$style['trash-icon']"
              @click="$emit('remove', item)"
            />
          </span>
        </li>
      </ul>
    </div>
    <div :class="$style.aside">
      <div
        :class="[$style.sort, sortBy === 'value' ? $style.active : '']"
        @click="sortBy = 'value'"
      >
        Sort by Value
        <span :class="$style.pointer"></span>
      </div>
      <div
        :class="[$style.sort, sortBy === 'addedDate' ? $style.active : '']"
        @click="sortBy = 'addedDate'"
      >
        Sort by Added Date
        <span :class="$style.pointer"></span>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, PropType, ref, watch } from 'vue';
import { format, formatDistance, formatRelative, subDays } from 'date-fns';
import _ from 'lodash';
import Item from '../types/Item';
import OrderBy from '../types/OrderBy';
import TrashIcon from './icons/TrashIcon.vue';
import CheckIcon from './icons/CheckIcon.vue';

export default defineComponent({
  name: 'ListItem',
  components: {
    TrashIcon,
    CheckIcon,
  },
  props: {
    search: {
      type: String,
      required: true,
    },
    listItems: {
      type: Array as PropType<Item[]>,
      required: true,
    },
  },
  setup(props, { emit }) {
    const sortBy = ref<OrderBy>('addedDate');

    const sortedItems = computed(() => {
      return _.orderBy(
        props.listItems,
        function (obj) {
          return obj[sortBy.value].toLowerCase();
        },
        [sortBy.value === 'value' ? 'asc' : 'desc']
      );
    });

    const itemsFiltered = computed(() => {
      return [...sortedItems.value].filter((item: Item) => {
        return item.value.toLowerCase().includes(props.search.toLowerCase());
      });
    });

    watch(itemsFiltered, () => {
      emit('foundItems', itemsFiltered.value.length);
    });

    let time = new Date();
    const timer = ref(time.toISOString());

    return { itemsFiltered, timer, sortBy, sortedItems };
  },

  methods: {
    formatDate(date: string) {
      return formatDistance(new Date(date), new Date(), {
        addSuffix: true,
        includeSeconds: true,
      });
    },
    lowerCase(string: string) {
      return string.toLowerCase();
    },
  },
});
</script>

<style lang="scss" module>
@use 'sass/color';

.listView {
  display: flex;
  flex-direction: row;
  gap: 50px;

  .main {
    flex: 4;
  }

  .aside {
    flex: 1;
    cursor: pointer;
  }
  .sort {
    padding: 12px;
    width: 100%;
    margin-bottom: 5px;
    border-radius: 6px;
    display: inline-flex;
    justify-content: space-between;

    .pointer {
      background: color.$icon-mountain-meadow;
      border-radius: 100%;
      display: none;
      height: 4px;
      margin: 6px 4px 9px 0;
      vertical-align: middle;
      width: 4px;
    }
  }

  .aside .active {
    background: color.$white;

    * {
      display: inline-flex;
    }
  }
}

.list .listItem {
  padding: 20px;
  display: inline-flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  border-bottom: 1px solid color.$bright-gray;
}
small {
  display: block;
}
.trash-icon {
  display: none;
  margin-left: 30px;
  cursor: pointer;
}

.listItem:hover {
  background: color.$white;
  border-radius: 6px;

  .trash-icon {
    display: inline-flex;
  }
}

.move {
  vertical-align: super;
}

.valueContainer {
  display: flex;
  .main-value {
    margin-left: 20px;
  }
}
.check {
  color: color.$icon-mountain-meadow;
}
</style>
