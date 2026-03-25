<script setup>
import JobListing from "@/components/JobListing.vue";
import { RouterLink } from "vue-router";
import { reactive, onMounted } from "vue";
//import { PulseLoader } from "vue-spinner";
import axios from "axios";

const { limit, showButton } = defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false,
  },
});

const state = reactive({
  jobs: [],
  isLoading: true,
});

onMounted(async () => {
  try {
    const response = await axios.get(
      "https://69c204777518bf8facbd1052.mockapi.io/jobs",
    );
    state.jobs = response.data;
  } catch (error) {
    console.error("Error fetching jobs:", error);
  } finally {
    state.isLoading = false;
  }
});
</script>

<template>
  <section class="bg-blue-50 px-4 py-10">
    <div class="container-xl lg:container m-auto">
      <h2 class="text-3xl font-bold text-green-500 text-center mb-10">
        Browse Job Listings
      </h2>
      <!-- SHOW LOADING SPINNER WHILE LOADING IS TRUE -->
      <div
        v-if="state.isLoading"
        class="fixed inset-0 flex items-center justify-center bg-black/10 z-50"
      >
        <div
          class="bg-white w-32 h-32 rounded-xl shadow flex items-center justify-center"
        >
          <div class="flex space-x-2">
            <div class="dot"></div>
            <div class="dot delay-200"></div>
            <div class="dot delay-400"></div>
          </div>
        </div>
      </div>
      <!-- SHOW JOB LISTINGS WHEN LOADING -->
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <JobListing
          v-for="job in state.jobs.slice(0, limit || state.jobs.length)"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>

  <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
    <RouterLink
      to="/jobs"
      class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
    >
      View All Jobs
    </RouterLink>
  </section>
</template>
<style scoped>
.dot {
  width: 10px;
  height: 10px;
  background-color: #ef4444;
  border-radius: 9999px;
  animation: bounce 1s infinite;
}

.delay-200 {
  animation-delay: 0.2s;
}

.delay-400 {
  animation-delay: 0.4s;
}

@keyframes bounce {
  0%,
  80%,
  100% {
    transform: scale(0);
    opacity: 0.3;
  }
  40% {
    transform: scale(1);
    opacity: 1;
  }
}
</style>
