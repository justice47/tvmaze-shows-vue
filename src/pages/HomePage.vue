<script setup lang="ts">
  import { onMounted, ref, computed, watch } from 'vue';
  import { useRouter, useRoute } from 'vue-router'
  import axios from 'axios';
  import ShowsHorizontalList from '@/components/ShowsHorizontalList.vue';
  import SectionTitle from '@/components/SectionTitle.vue'
  import CancelIcon from '@/components/icons/CancelIcon.vue';

  const router = useRouter()
  const route = useRoute()
  const initialData = ref([]);
  const showsForQuery = ref([]);
  const searchInput = ref('');
  const showSearchResults = ref(false);

  const client = axios.create({
    baseURL: 'https://api.tvmaze.com/',
  });

  const scienceFictionShows = computed(() => {
    return initialData.value.filter((show: any) => {
      return show.genres.includes('Science-Fiction');
    });
  })

  const comedyShows = computed(() => {
    return initialData.value.filter((show: any) => {
      return show.genres.includes('Comedy');
    });
  })

  watch(() => route.query.search, async () => {
    setSearchFromQuery();
  })

  onMounted(async () => {
    fetchInitialShows();

    setSearchFromQuery();
  });

  const setSearchFromQuery = async() => {
    if (route.query.search) {
      searchInput.value = route.query.search as string;
      await fetchShows();
    }
  }

  const fetchInitialShows = async () => {
    try {
      const { data } = await client.get('shows');

      initialData.value = data;
    } catch (error) {
      console.log(error);
    }
  }

  const searchClick = () => {
    if (searchInput.value !== '') {
      router.push({
        query: {
          search: searchInput.value
        }
      })
    }
  }

  const fetchShows = async () => {
    try {
      showSearchResults.value = true;

      const { data } = await client.get(`search/shows?q=${searchInput.value}}`);

      showsForQuery.value = data.map((show : any) => show.show);
    } catch (error) {
      console.log(error);
    }
  }

  const resetSearch = () => {
    showsForQuery.value = [];
    searchInput.value = '';
    showSearchResults.value = false;

    router.push({
      query: undefined
    })

    fetchInitialShows();
  }
</script>

<template>
  <main class="grid justify-items-center gap-8">
    <div class="p-4 lg:p-0 w-full lg:w-3/5">   
      <label for="default-search" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-white">Search</label>
      <div class="relative">
          <div class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
            <svg aria-hidden="true" class="w-5 h-5 text-gray-500 dark:text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
          </div>
          <input v-model="searchInput" type="search" id="default-search" class="block w-full p-4 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="Search shows" required>
          <div class="absolute right-2.5 bottom-2.5 grid grid-flow-col gap-2 items-center">
            <CancelIcon v-if="showSearchResults" class="cursor-pointer" @click="resetSearch" />
            <button class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800" @click="searchClick">Search</button>
          </div>
      </div>
    </div>

    <div class="grid w-full gap-8">
      <template v-if="!showSearchResults">
        <section class="grid">
          <SectionTitle>
            Science Fiction
          </SectionTitle>

          <ShowsHorizontalList :data="scienceFictionShows"/>
        </section>

        <section class="grid">
          <SectionTitle>
            Comedy
          </SectionTitle>

          <ShowsHorizontalList :standard-direction="false" :data="comedyShows" />
        </section>
      </template>

      <template v-else>
        <section class="grid">
          <SectionTitle>
            Search results:
          </SectionTitle>

          <ShowsHorizontalList :data="showsForQuery" />
        </section>
      </template>
    </div>
  </main>
</template>
