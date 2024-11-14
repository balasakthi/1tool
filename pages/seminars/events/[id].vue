<template>
  <v-breadcrumbs v-if="events" :items="items" divider="-">
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

  <v-row v-if="events">
    <v-col cols="12" md="6">
      <v-expansion-panels
        class="border"
        v-model="eventsOuterPanel"
        elevation="0"
      >
        <v-expansion-panel>
          <v-expansion-panel-title class="text-primary text-uppercase"
            >Event Details</v-expansion-panel-title
          >
          <v-expansion-panel-text class="text-h6 text-primary text-uppercase">
            {{ events.title }}
            <v-divider class="mt-3"></v-divider>

            <v-expansion-panels
              v-model="eventsInnerPanel"
              multiple
              elevation="0"
            >
              <v-expansion-panel>
                <v-expansion-panel-title class="text-primary text-uppercase"
                  >Master Data</v-expansion-panel-title
                >
                <v-expansion-panel-text>
                  <v-table class="text-subtitle-1">
                    <tbody>
                      <tr>
                        <td>Title</td>
                        <td>{{ events.title }}</td>
                      </tr>
                      <tr>
                        <td>Start Date</td>
                        <td>{{ events.start_time }}</td>
                      </tr>
                      <tr>
                        <td>End Date</td>
                        <td>{{ events.end_time }}</td>
                      </tr>
                      <tr>
                        <td>Contact persons</td>
                        <td>{{ events.contact_id }}</td>
                      </tr>
                      <tr>
                        <td>Duration (days)</td>
                        <td>{{ events.duration }}</td>
                      </tr>
                      <tr>
                        <td>Credits</td>
                        <td>{{ events.credits }}</td>
                      </tr>
                      <tr>
                        <td>All day</td>
                        <td>
                          {{ events.is_all_day }}
                        </td>
                      </tr>
                      <tr>
                        <td>ID</td>
                        <td>{{ events.id }}</td>
                      </tr>

                      <tr>
                        <td>Creation Date</td>
                        <td>{{ events.creation_time }}</td>
                      </tr>
                      <tr>
                        <td>Created by</td>
                        <td>{{ events.user_id }}</td>
                      </tr>
                    </tbody>
                  </v-table>

                  <v-divider></v-divider>
                </v-expansion-panel-text>
              </v-expansion-panel>

              <v-expansion-panel>
                <v-expansion-panel-title class="text-uppercase text-primary"
                  >Event Details</v-expansion-panel-title
                >
                <v-expansion-panel-text>
                  <v-table>
                    <tbody>
                      <tr>
                        <td>Organizer</td>
                        <td>{{ events.organizer }}</td>
                      </tr>
                      <tr>
                        <td>Street</td>
                        <td>{{ events.street }}</td>
                      </tr>
                      <tr>
                        <td>Zip</td>
                        <td>{{ events.zip }}</td>
                      </tr>
                      <tr>
                        <td>State</td>
                        <td>{{ events.state }}</td>
                      </tr>
                      <tr>
                        <td>Country</td>
                        <td>{{ events.country }}</td>
                      </tr>
                      <tr>
                        <td>Min. participants</td>
                        <td>{{ events.min_participants }}</td>
                      </tr>
                      <tr>
                        <td>Max. participants</td>
                        <td>{{ events.max_participants }}</td>
                      </tr>
                    </tbody>
                  </v-table></v-expansion-panel-text
                >
              </v-expansion-panel>
            </v-expansion-panels>
          </v-expansion-panel-text>
        </v-expansion-panel>
      </v-expansion-panels>
    </v-col>
  </v-row>
  <v-row class="mt-5" v-else>
    <v-col cols="12" md="6">
      <v-skeleton-loader type="article, paragraph"></v-skeleton-loader>
    </v-col>
  </v-row>
</template>
<script setup>
import { ref, onMounted } from "vue";
import { useUserStore } from "~/stores/user";

const userStore = useUserStore();

const route = useRoute();

const events = ref();

const eventsOuterPanel = ref(0);
const eventsInnerPanel = ref([0, 1]);

definePageMeta({
  middleware: ["auth"],
});

const items = ref([]);

onMounted(async () => {
  try {
    const response = await $fetch(
      `https://my.1tool.com/suite/api/calendar-events/${route.params.id}`,
      {
        headers: {
          Authorization: `Bearer ${userStore.token}`,
        },
      },
    );
    events.value = response.data;

    items.value = [
      {
        title: "Seminars",
        disabled: false,
        href: "/seminars",
      },
      {
        title: "Events",
        disabled: true,
        href: "",
      },
      {
        title: events.value.title || route.params.id,
        disabled: true,
        href: "",
      },
    ];
  } catch (error) {
    console.error("Failed to fetch courses:", error);
  }
});

useSeoMeta({
  title: "Events - 1Tool Freelancer App",
});
</script>
