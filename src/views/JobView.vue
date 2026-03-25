<script setup>
//import { PulseLoader } from "vue-spinner";
import BackButton from "@/components/BackButton.vue";
import { reactive, onMounted } from "vue";
import { useRoute, useRouter, RouterLink } from "vue-router";
import { useToast } from "vue-toastification";
import axios from "axios";

// ambil route & router
const route = useRoute();
const router = useRouter();
const toast = useToast();

// ambil id dari URL
const jobId = route.params.id;

// state
const state = reactive({
  job: null,
  isLoading: true,
});
const deleteJob = async () => {
  try {
    const confirm = window.confirm("Are you sure you want to delete this job?");
    if (confirm) {
      await axios.delete(
        `https://69c204777518bf8facbd1052.mockapi.io/jobs/${jobId}`,
      );
      alert("Job berhasil dihapus");
      router.push("/jobs");
    }
  } catch (error) {
    console.error("Error deleting job:", error);
    toast.error("Failed to delete job");
  }
};
// fetch data
onMounted(async () => {
  try {
    const response = await axios.get(
      `https://69c204777518bf8facbd1052.mockapi.io/jobs/${jobId}`,
    );

    // kalau data kosong
    if (!response.data) {
      throw new Error("Job not found");
    }

    state.job = response.data;
  } catch (error) {
    console.error("Error fetching job:", error);

    // redirect ke NotFound
    router.push("/not-found");
  } finally {
    state.isLoading = false;
  }
});
</script>

<template>
  <BackButton />
  <section v-if="!state.isLoading" class="bg-green-50">
    <div class="container m-auto py-10 px-6">
      <div class="grid grid-cols-1 md:grid-cols-[70%_30%] w-full gap-6">
        <main>
          <div
            class="bg-white p-6 rounded-lg shadow-md text-center md:text-left"
          >
            <div class="text-gray-500 mb-4">{{ state.job.type }}</div>
            <h1 class="text-3xl font-bold mb-4">{{ state.job.title }}</h1>
            <div
              class="text-gray-500 mb-4 flex align-middle justify-center md:justify-start"
            >
              <i class="pi pi-map-marker text-xl text-orange-700 mr-2"></i>
              <p class="text-orange-700">{{ state.job.location }}</p>
            </div>
          </div>

          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-green-800 text-lg font-bold mb-6">
              Job Description
            </h3>

            <p class="mb-4">
              {{ state.job.description }}
            </p>

            <h3 class="text-green-800 text-lg font-bold mb-2">Salary</h3>

            <p class="mb-4">{{ state.job.salary }} / Year</p>
          </div>
        </main>

        <!-- Sidebar -->
        <aside>
          <!-- Company Info -->
          <div class="bg-white p-6 rounded-lg shadow-md overflow-hidden">
            <h3 class="text-xl font-bold mb-6">Company Info</h3>

            <h2 class="text-2xl">{{ state.job.company.name }}</h2>

            <p class="my-2">
              {{ state.job.company.description }}
            </p>

            <hr class="my-4" />

            <h3 class="text-xl">Contact Email:</h3>

            <p
              class="my-2 bg-green-100 p-2 font-bold break-all text-sm md:text-base"
            >
              {{ state.job.company.contactEmail }}
            </p>

            <h3 class="text-xl">Contact Phone:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ state.job.company.contactPhone }}
            </p>
          </div>

          <!-- Manage -->
          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-xl font-bold mb-6">Manage Job</h3>
            <RouterLink
              :to="`/jobs/edit/${state.job.id}`"
              class="bg-green-500 hover:bg-green-600 text-white text-center font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
              >Edit Job</RouterLink
            >
            <button
              @click="deleteJob"
              class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
            >
              Delete Job
            </button>
          </div>
        </aside>
      </div>
    </div>
  </section>
  <div
    v-else
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
