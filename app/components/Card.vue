<template>
  <div
    class="relative bg-white shadow-md rounded-2xl overflow-hidden transition-all hover:shadow-xl hover:-translate-y-1 cursor-pointer border border-gray-100"
  >
    <div class="p-5 space-y-2">
      <h3 class="text-lg font-semibold text-gray-800">
        {{ title }}
      </h3>

      <h4 v-if="subtitle" class="text-sm text-indigo-600 font-medium">
        {{ subtitle }}
      </h4>

      <p v-if="description" class="text-gray-500 text-sm">
        {{ description }}
      </p>

      <div class="flex items-center justify-between mt-3">
        <button
          v-if="actionText"
          @click.stop="onAction"
          class="px-3 py-1.5 text-sm bg-indigo-600 text-white rounded-xl transition hover:bg-indigo-700"
        >
          {{ actionText }}
        </button>

        <div v-if="badge">
          <span
            class="px-2 py-1 text-xs bg-indigo-600 text-white rounded-full shadow"
          >
            {{ badge }}
          </span>
        </div>
      </div>
    </div>

    <div
      class="absolute inset-0 bg-indigo-200 opacity-0 pointer-events-none transition-opacity hover:opacity-10"
    />
  </div>
</template>

<script lang="ts" setup>
export interface CardProps {
  title: string;
  subtitle?: string;
  description?: string;
  badge?: string;
  actionText?: string;
  onAction?: () => void;
}
const props = withDefaults(defineProps<CardProps>(), {});
</script>
