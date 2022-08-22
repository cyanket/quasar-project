<template>
  <div>
    <q-tabs v-model="tab" dense class="text-grey bg-purple shadow-2" active-color="primary"
      indicator-color="cyan" align="justify" narrow-indicator v-if="!searchActive">
      <q-tab name="table" label="Data Entries" class="text-white"/>
      <q-tab name="cards" label="Cards" class="text-white"/>
      <q-tab name="settings" label="Settings" class="text-white"/>
      <q-btn class="q-mr-md" icon="search" round unelevated @click="toggleSearch"/>
    </q-tabs>
    <q-tabs v-if="searchActive">
      <local-search-input v-if="searchActive" @input="updateSearch" @click="toggleSearch"
        class="app-panel-header__search" />
    </q-tabs>

    <q-separator />

    <q-tab-panels v-model="tab" class="darkPage" animated>
      <q-tab-panel name="table">
        <q-table color="secondary" :loading="loading" :rows="entries" :columns="columns"
        v-model:pagination="pagination" class="my-sticky-column-table"/>
      </q-tab-panel>

      <q-tab-panel name="cards">
        <div class="q-pa-md row items-start q-gutter-md justify-center">
        <div v-for="card in cardData" v-bind:key="card.id">
        <q-card class="my-card">
          <q-img :src="card.img" />
          <q-card-section>
            <q-btn fab color="green" icon="place" class="absolute"
              style="top: 0; right: 12px; transform: translateY(-50%);"/>
            <div class="row no-wrap items-center">
              <div class="col text-h6 ellipsis">{{card.name}}</div>
              <div class="col-auto text-grey text-caption q-pt-md row no-wrap items-center">
                <q-icon name="place" />
                {{card.distance}}
              </div>
            </div>
            <q-rating v-model="stars" class="star-rating" :max="5" size="32px" />
          </q-card-section>
          <q-card-section class="q-pt-none">
            <div class="text-subtitle1">{{card.label}}</div>
            <div class="text-caption text-grey">{{card.title}}</div>
          </q-card-section>
          <q-separator />
          <q-card-actions class="full-width block">
            <q-btn flat round icon="event" class="q-ml-xs" />
            <q-btn flat color="primary">Reserve</q-btn>
            <q-btn round flat icon="delete" class="float-right q-mr-xs"
            @click="deleteItem(card.id)"/>
          </q-card-actions>
        </q-card>
        </div>
        </div>
      </q-tab-panel>

      <q-tab-panel name="settings">
        <div id="app" :class="darkDark">
          <div class="flex">
            <h3>Toggle Dark Mode</h3>
            <div class="mode-toggle" @click="modeToggle" :class="darkDark">
              <div class="toggle">
                <div id="dark-mode" type="checkbox"></div>
              </div>
            </div>
          </div>
        </div>
      </q-tab-panel>
    </q-tab-panels>

  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';
import LocalSearchInput from 'src/components/LocalSearchInput.vue';

export default {
  components: { LocalSearchInput },
  el: '#app',
  data() {
    const cardData = [
      {
        id: 0, name: 'Cafe Barista', label: 'Italian Cafe', title: 'Small plates., salads & sandwiches in an intimate setting .', img: 'https://cdn.quasar.dev/img/chicken-salad.jpg', distance: '250 ft',
      },
      {
        id: 1, name: 'Yum Foods', label: 'Indian Restaurant', title: 'North Indian, Chaat, Shakes, Appetizers & nice ambience.', img: 'https://cdn.pixabay.com/photo/2014/04/05/11/27/buffet-315691_1280.jpg', distance: '1 km',
      },
      {
        id: 2, name: 'Momo Kings', label: 'Nepalese Cuisine', title: 'Small plates, Momos & Chinese in global retaurant chain.', img: 'https://cdn.pixabay.com/photo/2020/09/21/12/40/meal-5589923_1280.jpg', distance: '1.6 km',
      },
      {
        id: 3, name: 'Haldiram Planet Food', label: 'Indian Food', title: 'Small plates., salads & sandwiches in an intimate setting .', img: 'https://cdn.pixabay.com/photo/2014/12/22/12/22/food-577224_1280.jpg', distance: '3.6 km',
      },
      {
        id: 4, name: 'Burger Singh', label: 'Continental, Fast-food', title: 'Fast food, Continental., Shakes., Appetizers & nice decor.', img: 'https://cdn.pixabay.com/photo/2016/05/25/10/43/hamburger-1414422_1280.jpg', distance: '1.9 km',
      },
      {
        id: 5, name: 'Mustard Restro Lounge', label: 'Continental, Italian', title: 'Continental, Italian, & Chinese in global retaurant chain.', img: 'https://cdn.pixabay.com/photo/2017/01/26/02/06/platter-2009590_1280.jpg', distance: '2.1 km',
      },
    ];
    const loading = ref(true);
    const entries = ref([]);
    const columns = [
      {
        name: 'api', label: 'API', field: 'API', align: 'left',
      },
      {
        name: 'description', label: 'Description', field: 'Description', align: 'left',
      },
      {
        name: 'link', label: 'Link', field: 'Link', align: 'left',
      },
      {
        name: 'category', label: 'Category', field: 'Category', align: 'left',
      },
    ];
    const pagination = ref({
      sortBy: 'name',
      descending: false,
      page: 1,
      rowsPerPage: 0,
    });

    // Fetch dogs
    axios.get('https://api.publicapis.org/entries')
      .then((response) => {
        entries.value = response.data.entries;
      })
      .finally(() => {
        loading.value = false;
      });
    return {
      tab: ref('table'),
      columns,
      cardData,
      loading,
      entries,
      pagination,
      darkMode: false,
      stars: ref(4),
      searchActive: false,
      searchText: '',
    };
  },
  methods: {
    deleteItem(id) {
      const newArray = this.cardData.filter((card) => card.id !== id);
      this.cardData = [...newArray];
    },
    toggleSearch() {
      this.searchActive = !this.searchActive;
      if (this.searchActive === false) {
        this.searchText = '';
      }
    },
    updateSearch(value) {
      this.searchText = value;
    },

    dark() {
      document.querySelector('body').classList.add('dark-mode');
      this.darkMode = true;
      this.$emit('dark');
    },

    light() {
      document.querySelector('body').classList.remove('dark-mode');
      this.darkMode = false;
      this.$emit('light');
    },

    modeToggle() {
      if (this.darkMode || document.querySelector('body').classList.contains('dark-mode')) {
        this.light();
      } else {
        this.dark();
      }
    },
  },
  computed: {
    darkDark() {
      return this.darkMode && 'darkmode-toggled';
    },
    cardData1() {
      return this.cardData;
    },
  },
};
</script>
<style lang="scss">
$dark: #171717;
$dark-cardbg: #49494e;
$light: #ededed;
$mode-toggle-bg: #262626;

body {
  background-color: #fff;
  color: $dark;
  transition: background-color .2s ease, color .2s ease;
}

body {
  &.dark-mode {
    background-color: lighten($dark, 5%);
    .flex {
      h1 {
        color: #fff;
      }
    }
  }
}

.mode-toggle {
  position: relative;
  padding: 0;
  width: 44px;
  height: 24px;
  min-width: 36px;
  min-height: 20px;
  background-color: $mode-toggle-bg;
  border: 0;
  border-radius: 24px;
  outline: 0;
  overflow: hidden;
  cursor: pointer;
  z-index: 2;
  -webkit-tap-highlight-color: rgba(0,0,0,0);
  -webkit-touch-callout: none;
  appearance: none;
  transition: background-color .5s ease;

  .toggle {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    margin: auto;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 3px solid transparent;
    box-shadow: inset 0 0 0 2px #a5abba;
    overflow: hidden;
    transition: transform .5s ease;

    #dark-mode {
      position: relative;
      width: 100%;
      height: 100%;
      overflow: hidden;
      border-radius: 50%;

      &:before {
        content: '';
        position: relative;
        width: 100%;
        height: 100%;
        left: 50%;
        float: left;
        background-color: #a5abba;
        transition: border-radius .5s ease,
        width .5s ease,
        height .5s ease, left .5s ease,
        transform .5s ease;
      }
    }
  }
}

body.dark-mode {
  .mode-toggle {
    background-color: lighten($mode-toggle-bg, 5%);
    .toggle {
      transform: translateX(19px);
      #dark-mode {
        &:before {
          border-radius: 50%;
          width: 150%;
          height: 85%;
          left: 40%;
          transform: translate(-10%, -40%), rotate(-35deg);
        }
      }
    }
  }
}
.flex {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}
.my-sticky-column-table {
  height: 88vh;

  td:first-child {
    /* bg color is important for td; just specify one */
    background-color: #c1f4cd !important
  }

  tr th {
    position: sticky;
    /* higher than z-index for td below */
    z-index: 2;
    background: #fff;
  }

  /* this will be the loading indicator */
  thead tr:last-child th {
    /* height of all previous header rows */
    top: 48px;
    /* highest z-index */
    z-index: 3;
  }
  thead tr:first-child th {
    top: 0;
    z-index: 1;
  }
  tr:first-child th:first-child {
    /* highest z-index */
    z-index: 3;
  }

  td:first-child {
    z-index: 1;
  }

  td:first-child, th:first-child  {
    position: sticky;
    left: 0;
  }
}
.star-rating {
  display: flex !important;
  flex-wrap: nowrap;
  flex-direction: row;
}
.dark-mode .darkPage {
  background: #242424;
  color: $light;
  transition: background-color .2s ease, color .2s ease;
}
.dark-mode .my-card {
  background: $dark-cardbg;
  color: $light;
}
.dark-mode .q-table__container {
  background: $dark-cardbg;
  color: $light;
  transition: background-color .2s ease, color .2s ease;
}
.dark-mode tr th {
  background: $dark-cardbg;
  color: $light;
}
.dark-mode td:first-child {
  background: #636466 !important;
  color: $light;
}
</style>
