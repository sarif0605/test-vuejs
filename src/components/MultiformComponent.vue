<template>
  <div class="w-full max-w-lg mx-auto p-5">
    <h2 class="text-2xl font-bold mb-4" v-if="!isSubmitted">
      {{ currentStep.title }}
    </h2>
    <p class="text-gray-600 mb-6" v-if="!isSubmitted">
      {{ currentStep.description }}
    </p>

    <!-- Form Fields -->
    <form v-if="!isSubmitted" @submit.prevent="handleSubmit">
      <div
        v-for="(field, index) in currentStep.fields"
        :key="index"
        class="mb-4"
      >
        <!-- Text Field -->
        <div v-if="field.type === 'textfield'">
          <label class="block font-semibold">{{ field.label }}</label>
          <input
            type="text"
            v-model="formData[field.label]"
            :placeholder="field.placeholder"
            :required="field.required"
            class="w-full px-3 py-2 border rounded"
          />
        </div>

        <!-- Radio Field -->
        <div v-if="field.type === 'radio'">
          <label class="block font-semibold mb-2">{{ field.label }}</label>
          <div
            v-for="option in field.options"
            :key="option.value"
            class="flex items-center mb-2"
          >
            <input
              type="radio"
              :value="option.value"
              v-model="formData[field.label]"
              :required="field.required"
              class="mr-2"
            />
            <span>{{ option.label }}</span>
          </div>
        </div>

        <!-- Text Area -->
        <div v-if="field.type === 'textarea'">
          <label class="block font-semibold">{{ field.label }}</label>
          <textarea
            v-model="formData[field.label]"
            :placeholder="field.placeholder"
            :required="field.required"
            class="w-full px-3 py-2 border rounded"
          ></textarea>
        </div>

        <!-- Autocomplete -->
        <div v-if="field.type === 'autocomplete'">
          <label class="block font-semibold">{{ field.label }}</label>
          <select
            v-model="formData[field.label]"
            :required="field.required"
            class="w-full px-3 py-2 border rounded"
          >
            <option value="" disabled>{{ field.placeholder }}</option>
            <option
              v-for="option in field.options"
              :key="option"
              :value="option"
            >
              {{ option }}
            </option>
          </select>
        </div>
      </div>

      <!-- Navigation Buttons -->
      <div class="flex justify-between mt-6">
        <button
          v-if="currentStepIndex > 0"
          type="button"
          @click="prevStep"
          class="px-4 py-2 bg-gray-400 text-white rounded hover:bg-gray-500"
        >
          Previous
        </button>
        <button
          v-if="currentStepIndex < steps.length - 1"
          type="button"
          @click="nextStep"
          class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
        >
          Next
        </button>
        <button
          v-if="currentStepIndex === steps.length - 1"
          type="submit"
          class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700"
        >
          Submit
        </button>
      </div>
    </form>

    <!-- Form Summary Component -->
    <FormSummary v-if="isSubmitted" :formData="formData" @goBack="resetForm" />

    <!-- Display Submitted Data Array -->
    <div class="mt-8">
      <h3 class="text-xl font-semibold mb-4">Submitted Data</h3>
      <ul v-if="submittedData.length">
        <li
          v-for="(data, index) in submittedData"
          :key="index"
          class="p-4 mb-4 border rounded bg-gray-50"
        >
          <h4 class="font-semibold">Submission {{ index + 1 }}</h4>
          <ul>
            <li v-for="(value, key) in data" :key="key">
              {{ key }}: {{ value }}
            </li>
          </ul>
        </li>
      </ul>
      <p v-else>No submissions yet.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import FormSummary from "./FormSummary.vue";

const steps = [
  {
    step: 1,
    title: "Personal Information",
    description: "Please fill out your personal information",
    fields: [
      {
        type: "textfield",
        label: "Name",
        placeholder: "Enter your name",
        required: true,
      },
      {
        type: "radio",
        label: "Gender",
        options: [
          { label: "Male", value: "male" },
          { label: "Female", value: "female" },
          { label: "Other", value: "other" },
        ],
        required: true,
      },
    ],
  },
  {
    step: 2,
    title: "Additional Information",
    description: "Please provide additional details",
    fields: [
      {
        type: "textarea",
        label: "Description",
        placeholder: "Enter a description",
        required: false,
      },
      {
        type: "autocomplete",
        label: "Title",
        placeholder: "Enter a title",
        options: ["Mr.", "Mrs.", "Ms.", "Dr.", "Prof."],
        required: true,
      },
    ],
  },
];

const currentStepIndex = ref(0);
const formData = ref({});
const isSubmitted = ref(false);
const submittedData = ref([]);

const currentStep = computed(() => steps[currentStepIndex.value]);

const nextStep = () => {
  if (currentStepIndex.value < steps.length - 1) currentStepIndex.value++;
};

const prevStep = () => {
  if (currentStepIndex.value > 0) currentStepIndex.value--;
};

const handleSubmit = () => {
  // Save data to submittedData array
  submittedData.value.push({ ...formData.value });
  // Mark form as submitted
  isSubmitted.value = true;
};

const resetForm = () => {
  // Go back to the first step and reset formData
  currentStepIndex.value = 0;
  formData.value = {};
  isSubmitted.value = false;
};
</script>
