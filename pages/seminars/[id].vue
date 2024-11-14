<template>
  <v-breadcrumbs v-if="course" :items="items" divider="-">
    <template v-slot:item="{ item }">
      <v-breadcrumbs-item
        class="text-primary"
        :to="item.href"
        :disabled="item.disabled"
      >
        {{ item.title }}
      </v-breadcrumbs-item>
    </template>
  </v-breadcrumbs>
  <v-row v-if="course">
    <v-col cols="12" md="6">
      <v-expansion-panels
        class="border"
        v-model="coursesOuterPanel"
        elevation="0"
      >
        <v-expansion-panel>
          <v-expansion-panel-title class="text-uppercase text-primary"
            >Seminar Data</v-expansion-panel-title
          >
          <v-expansion-panel-text class="text-h6 text-primary text-uppercase">
            {{ course.title }}
            <v-divider class="mt-3"></v-divider>

            <v-expansion-panels
              v-model="coursesInnerPanel"
              multiple
              elevation="0"
            >
              <v-expansion-panel>
                <v-expansion-panel-title class="text-uppercase text-primary"
                  >Master Data</v-expansion-panel-title
                >
                <v-expansion-panel-text>
                  <v-table class="text-subtitle-1">
                    <tbody>
                      <tr>
                        <td>Title</td>
                        <td>{{ course.title }}</td>
                      </tr>
                      <tr>
                        <td>Subtitle</td>
                        <td>{{ course.subtitle }}</td>
                      </tr>
                      <tr>
                        <td>Shorthand symbol</td>
                        <td>{{ course.number }}</td>
                      </tr>
                      <tr>
                        <td>Seminar Number</td>
                        <td>{{ course.max_number }}</td>
                      </tr>
                      <tr>
                        <td>ID</td>
                        <td>{{ course.id }}</td>
                      </tr>
                      <tr>
                        <td>Creation Date</td>
                        <td>
                          {{
                            new Date(course.created_at).toLocaleString(
                              "en-US",
                              {
                                year: "numeric",
                                month: "short",
                                day: "numeric",
                                hour: "2-digit",
                                minute: "2-digit",
                                timeZoneName: "short",
                              },
                            )
                          }}
                        </td>
                      </tr>
                    </tbody>
                  </v-table>

                  <v-divider></v-divider>
                </v-expansion-panel-text>
              </v-expansion-panel>

              <v-expansion-panel>
                <v-expansion-panel-title class="text-primary text-uppercase"
                  >Content & Goals</v-expansion-panel-title
                >
                <v-expansion-panel-text>
                  <h3 class="text-subtitle-1">Seminar Contents</h3>
                  <p v-html="course.contents"></p>
                </v-expansion-panel-text>
              </v-expansion-panel>
            </v-expansion-panels>
          </v-expansion-panel-text>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-col>
    <v-col cols="12" md="6">
      <v-expansion-panels class="border" v-model="eventsPanel" elevation="0">
        <v-expansion-panel>
          <v-expansion-panel-title class="text-primary text-uppercase"
            >Events ({{ course.events.length }})</v-expansion-panel-title
          >
          <v-expansion-panel-text class="text-h6 text-primary">
            <v-table>
              <thead class="text-uppercase">
                <tr>
                  <th class="text-left"></th>
                  <th class="text-left">Title</th>
                  <th class="text-left">Location</th>
                  <th class="text-left">Add People</th>
                  <th class="text-left">Start Date</th>
                  <th class="text-left">End Date</th>
                  <th class="text-left">All Day</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="event in course.events">
                  <td>
                    <v-container class="d-flex flex-row">
                      <v-icon
                        class="me-2 cursor-pointer"
                        color="primary"
                        size="small"
                        @click="navigateTo(`/seminars/events/${event.id}`)"
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
                        color="red"
                        size="small"
                      >
                        mdi-trash-can-outline
                      </v-icon>
                    </v-container>
                  </td>
                  <td>
                    {{ event.title }}
                  </td>
                  <td>{{ event.location }}</td>
                  <td>{{ event.min_participants }}</td>
                  <td>{{ event.start_time }}</td>
                  <td>{{ event.end_time }}</td>
                  <td>
                    <v-icon
                      :color="event.is_all_day ? 'success' : 'red'"
                      :icon="
                        event.is_all_day
                          ? 'mdi-check-circle'
                          : 'mdi-close-circle'
                      "
                      size="small"
                    ></v-icon>
                  </td>
                </tr>
              </tbody>
              <tbody></tbody>
            </v-table>
            <v-divider class="mt-3"></v-divider>
          </v-expansion-panel-text>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-col>
  </v-row>
  <v-row class="mt-5" v-else>
    <v-col cols="12" md="6">
      <v-skeleton-loader type="article, paragraph"></v-skeleton-loader>
    </v-col>
    <v-col cols="12" md="6">
      <v-skeleton-loader type="article"></v-skeleton-loader>
    </v-col>
  </v-row>
</template>
<script setup>
import { ref, onMounted } from "vue";
import { useUserStore } from "~/stores/user";

const userStore = useUserStore();

const route = useRoute();

const course = ref();

const coursesOuterPanel = ref(0);
const eventsPanel = ref(0);
const coursesInnerPanel = ref([0, 1]);

definePageMeta({
  middleware: ["auth"],
});

const items = ref([]);

onMounted(async () => {
  try {
    const response = await $fetch(
      `https://my.1tool.com/suite/api/courses/${route.params.id}?include=events`,
      {
        headers: {
          Authorization: `Bearer ${userStore.token}`,
        },
      },
    );
    course.value = response.data;

    items.value = [
      {
        title: "Seminars",
        disabled: false,
        href: "/seminars",
      },
      {
        title: course.value.title || route.params.id,
        disabled: true,
        href: "",
      },
    ];
  } catch (error) {
    console.error("Failed to fetch courses:", error);
  }
});

useSeoMeta({
  title: "Seminar - 1Tool Freelancer App",
});
</script>
