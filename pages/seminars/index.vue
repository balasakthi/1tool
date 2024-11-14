<template>
  <v-card flat>
    <v-tabs v-model="tab" color="primary">
      <v-tab value="all">All</v-tab>
      <v-container class="d-flex ga-2 flex-row align-center justify-end" fluid>
        <v-btn class="text-subtitle-1" variant="outlined" color="primary" flat
          >Columns</v-btn
        >
        <v-btn
          class="text-subtitle-1"
          variant="outlined"
          color="primary"
          flat
          prepend-icon="mdi-filter-outline"
          >Filter</v-btn
        >
        <v-btn class="text-subtitle-1" color="primary" flat>New Seminar</v-btn>
      </v-container>
    </v-tabs>

    <v-card-text>
      <v-tabs-window v-model="tab">
        <v-tabs-window-item value="all">
          <v-card flat>
            <template v-slot:text>
              <v-text-field
                v-model="search"
                label="Search"
                prepend-inner-icon="mdi-magnify"
                variant="outlined"
                hide-details
                single-line
              ></v-text-field>
            </template>
            <v-data-table
              :headers="headers"
              v-model="selected"
              v-model:expanded="expanded"
              :items="courseItems"
              :search="search"
              show-select
              show-expand
              :loading="isLoading"
            >
              <template v-slot:loading>
                <v-skeleton-loader type="table-row@5"></v-skeleton-loader>
              </template>
              <template v-slot:expanded-row="{ columns, item }">
                <tr>
                  <th colspan="2">Title</th>

                  <th>Start Date</th>
                  <th>End Date</th>
                  <th>Participants</th>
                </tr>
                <tr v-for="event in item.events">
                  <td colspan="2">
                    <NuxtLink
                      class="text-decoration-none text-primary"
                      :to="`/seminars/events/${event.id}`"
                      >{{ event.title }}</NuxtLink
                    >
                  </td>

                  <td>
                    {{ event.start_time }}
                  </td>
                  <td>
                    {{ event.end_time }}
                  </td>
                  <td>
                    {{ event.min_participants }}
                  </td>
                </tr>
              </template>
              <template v-slot:item.actions="{ item }">
                <v-container class="d-flex flex-row">
                  <v-icon
                    class="me-2 cursor-pointer"
                    color="primary"
                    size="small"
                    @click="navigateTo(`/seminars/${item.id}`)"
                  >
                    mdi-open-in-new
                  </v-icon>
                  <v-icon
                    class="me-2 cursor-pointer"
                    color="primary"
                    size="small"
                  >
                    mdi-content-copy
                  </v-icon>
                  <v-icon
                    class="me-2 cursor-pointer"
                    color="primary"
                    size="small"
                  >
                    mdi-file-export-outline
                  </v-icon>
                  <v-icon
                    class="me-2 cursor-pointer"
                    color="red"
                    size="small"
                    @click="deleteItem(item)"
                  >
                    mdi-trash-can-outline
                  </v-icon>
                </v-container>
              </template>
            </v-data-table>
          </v-card>
        </v-tabs-window-item>
      </v-tabs-window>
    </v-card-text>
  </v-card>
</template>

<script setup>
import { ref, onMounted, watch, computed } from "vue";
import { useUserStore } from "~/stores/user";

const userStore = useUserStore();

const tab = ref("all");
const courses = ref([]);
const search = ref("");
const expanded = ref([]);
const isLoading = ref(true);

definePageMeta({
  middleware: ["auth"],
});

const headers = [
  { title: "ID", value: "id" },
  { title: "Title", value: "title" },
  { title: "SubTitle", value: "subtitle" },
  { title: "Shorthand Symbol", value: "shorthandSymbol" },
  { title: "Actions", key: "actions", sortable: false },
];

onMounted(async () => {
  try {
    const response = await $fetch(
      "https://my.1tool.com/suite/api/courses?include=events",
      {
        headers: {
          Authorization: `Bearer ${userStore.token}`,
        },
      },
    );
    courses.value = response.data;
    isLoading.value = false;
  } catch (error) {
    console.error("Failed to fetch courses:", error);
  }
});

const courseItems = computed(() => {
  return courses.value.map((course) => ({
    ...course,
    shorthandSymbol: `${course.number}-${course.max_number}`,
  }));
});

function deleteItem(item) {
  console.log(item.id);
}

useSeoMeta({
  title: "Seminars - 1Tool Freelancer App",
});
</script>
