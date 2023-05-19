<script setup lang="ts">
const props = defineProps({
  title: {
    type: String,
    required: true,
  },
  data: {
    type: String,
    required: true,
  },
});
</script>

<template>
  <div
    class="border-2 border-zinc-700 text-white break-all w-full rounded-md flex flex-col pb-10 min-w-[300px]"
  >
    <span class="text-3xl p-10 select-none">{{ $props.title }}</span>
    <div class="">
      <div
        v-for="(vote, name) in $props.data"
        :key="name"
        class="flex flex-col px-10 py-3"
      >
        <div class="px-2 py-3">
          <div class="relative bg-inherit">
            <input
              type="text"
              class="peer bg-transparent h-10 w-full text-gray-200 placeholder-transparent ring-2 px-2 ring-zinc-600"
              :placeholder="vote"
              :value="vote"
              disabled
            /><label
              for="username"
              class="absolute cursor-text left-0 -top-3 text-sm text-gray-200 bg-inherit mx-1 px-1 peer-placeholder-shown:text-base peer-placeholder-shown:text-gray-500 peer-placeholder-shown:top-2 peer-focus:-top-3 peer-focus:text-sky-600 peer-focus:text-sm transition-all"
              >{{ name }}</label
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
