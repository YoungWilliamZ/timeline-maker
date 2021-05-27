<template>
  <div class="backdrop-filter backdrop-blur-sm">
    <div class="flex flex-row">
      <div class="underline flex items-end">
        <a href="https://github.com/kochrt/timeline-maker">Github</a>
      </div>
      <button
        class="ml-auto hover:bg-gray-500 rounded px-3 flex flex-row items-center"
        @click="collapsed = !collapsed"
      >
        {{ collapsed ? "Expand" : "Collapse" }}
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-3 w-3 ml-1"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          v-if="!collapsed"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M19 9l-7 7-7-7"
          />
        </svg>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-3 w-3"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          v-if="collapsed"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M5 15l7-7 7 7"
          />
        </svg>
      </button>
    </div>
    <div class="flex md:flex-row flex-col pt-3" v-show="!collapsed">
      <storage
        :list="list"
        @selected="selectedTimeline"
        @delete="deleteTimeline"
      />
      <div class="flex flex-col mb-3 text-black">
        <textarea
          class="border shadow-md flex-grow p-2"
          name="eventsField"
          cols="55"
          rows="12"
          v-model="events"
        ></textarea>
        <button
          class="bg-blue-100 mt-3 rounded shadow-md hover:shadow-none transition-all duration-100"
          @click="save"
        >
          Save timeline
        </button>
      </div>
      <timeline-maker :eventString="events" class="flex-grow mr-3 mb-3" />
    </div>
  </div>
</template>

<script lang="ts">
import Storage from "./Storage.vue";
import TimelineMaker from "./TimelineMaker.vue";

export default {
  components: { TimelineMaker, Storage },
  data() {
    return {
      collapsed: false,
      currentTimelineName: "",
      events: `09/2016-07/2020: CS degree
07/2020-now: dev @Tencent
`,
      list: [] as string[],
    };
  },
  mounted() {
    this.getTimelines();
  },
  methods: {
    deleteTimeline(name: string) {
      if (confirm(`Delete ${name}?`)) {
        localStorage.removeItem(name);
        this.list.splice(this.list.indexOf(name), 1);
        localStorage.setItem("timelines", this.list.join(","));
      }
    },
    selectedTimeline(name: string) {
      this.loadTimeline(name);
    },
    save() {
      const timelineName = prompt(
        "Save timeline as: ",
        this.currentTimelineName
      );
      if (timelineName) {
        localStorage.setItem(timelineName, this.events);
        if (this.list.includes(timelineName)) {
          return;
        }
        this.list.push(timelineName);
        localStorage.setItem("timelines", this.list.join(","));
      }
    },
    loadTimeline(name: string) {
      this.events = localStorage.getItem(name) ?? "";
      this.currentTimelineName = name;
    },
    getTimelines() {
      const concatenatedList = localStorage.getItem("timelines");
      if (concatenatedList) {
        this.list = concatenatedList.split(",");
        this.loadTimeline(this.list[0]);
      }
    },
  },
};
</script>

<style>
body {
  background-color: #384047;
  color: white;
  height: 100vh;
  margin: 0;
}
</style>