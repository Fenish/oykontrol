<script setup lang="ts">
const props = defineProps({
  chpData: {
    type: Object,
    required: true,
  },
});

const chpPanel = ref("");
const oyVeOtesiPanel = ref("");
const tutanakUrl = ref(undefined);

onMounted(async () => {
  const [il, ilce, sandik_no] = props.chpData.konum.split("/");
  const { data } = await useFetch(
    `https://afg.theyka.net/oyveotesi?&city_name=${il}&district_name=${ilce}&ballot_box_id=${sandik_no}`
  );

  const ovo_data: any = data.value;
  const oyveotesi_data: any = {
    "RECEP TAYYİP ERDOĞAN": ovo_data["recep_tayyip"],
    "MUHARREM İNCE": ovo_data["muharrem_ince"],
    "KEMAL KILIÇDAROĞLU": ovo_data["kemal_kilicdaroglu"],
    "SİNAN OĞAN": ovo_data["sinan_ogan"],
  };

  tutanakUrl.value = ovo_data["image_url"].replace("/raw/", "/blurred-min/");
  oyVeOtesiPanel.value = oyveotesi_data;
  chpPanel.value = props.chpData.adaylar;
});
</script>

<template>
  <div
    class="flex border-zinc-700 rounded-md justify-between m-10 mx-28 flex-col lg:flex-row gap-10 w-full"
    v-if="chpPanel != '' && oyVeOtesiPanel != ''"
  >
    <div class="w-full overflow-x-auto p-4">
      <div class="overflow-hidden min-w-max">
        <div
          class="grid grid-cols-4 p-4 text-sm font-medium border-t border-b gap-x-16 bg-zinc-700 border-gray-700 text-white"
        >
          <div class="flex items-center">Adaylar</div>
          <div>CHP Verisi</div>
          <div>Oy ve Ötesi Verisi</div>
          <div>Verilerin Uyuşması</div>
        </div>

        <div
          class="grid grid-cols-4 px-4 py-5 text-sm text-gray-700 border-b border-gray-200 gap-x-16 dark:border-gray-700"
          v-for="(vote, name) in oyVeOtesiPanel"
        >
          <div class="text-gray-500 dark:text-gray-400">
            {{ name }}
          </div>
          <span class="text-white">{{ chpPanel[name] }}</span>
          <span class="text-white">{{ vote }}</span>
          <div>
            <svg
              v-if="chpPanel[name] == vote"
              class="w-5 h-5 text-green-500"
              aria-hidden="true"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                clip-rule="evenodd"
              ></path>
            </svg>
            <svg
              v-else
              class="w-5 h-5 text-red-500"
              aria-hidden="true"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                clip-rule="evenodd"
              ></path>
            </svg>
          </div>
        </div>

        <div
          :v-if="tutanakUrl"
          class="flex py-5 pb-0 text-sm text-gray-700 border-gray-200 dark:border-gray-700"
        >
          <div>
            <a
              :href="tutanakUrl"
              target="_blank"
              class="px-12 py-3 text-white block w-full bg-green-600 hover:bg-green-700 font-medium rounded-lg text-sm text-center"
              >Tutanağı Görüntüle</a
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
