<script setup lang="ts">
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const tcKimlikNo = ref("");
useHead({
  title: ".:: Oy Kontrol ::. ",
  meta: [
    {
      hid: "description",
      name: "description",
      content:
        "Oy kontrol, chp ve ysk verilerini karşılaştıran bir uygulamadır. Bu site ile sandığınızda herhangi bir usulsüzlük olup olmadığını kontrol edebilirsiniz.",
    },
  ],
});

let chpData = ref(undefined);
async function sorgula() {
  chpData.value = undefined;

  if (tcKimlikNo.value.length != 11) {
    toast.error("TC Kimlik Numarası 11 haneli olmalıdır.", { theme: "dark" });
    return;
  }

  await useFetch(`https://afg.theyka.net/chpfetch?tckn=${tcKimlikNo.value}`, {
    onResponseError({ response, error }) {
      toast.error("Bir hata oluştu. Lütfen tekrar deneyin.", { theme: "dark" });
    },
    onResponse({ response }) {
      chpData.value = response._data;
    },
  });
  tcKimlikNo.value = "";
  // chpData.value = json;
}
</script>

<template>
  <div class="flex justify-center flex-col bg-zinc-800 min-h-screen">
    <div class="flex justify-center items-center flex-col">
      <div
        class="flex justify-center items-center flex-col"
        :class="chpData ? `mt-20` : ``"
      >
        <span class="text-white text-3xl select-none">TC Kimlik No</span>
        <span class="text-zinc-400 text-lg select-none">
          Sandığınızdaki oy sonuçlarını karşılaştırmak için tc kimlik numaranızı
          girin
        </span>
        <input
          type="text"
          v-model="tcKimlikNo"
          class="text-white mt-5"
          oninput="this.value = this.value.replace(/[^0-9]/g, '');"
          maxlength="11"
        />
        <button
          type="button"
          @click="sorgula"
          class="select-none mt-8 w-32 border font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2 border-blue-500 text-blue-500 hover:text-white hover:bg-blue-500"
        >
          Sorgula
        </button>
      </div>
      <div v-if="chpData" class="flex w-full justify-center">
        <Results :chpData="chpData" />
      </div>
      <div class="select-none mt-5">
        <a
          href="https://oyveotesi.org/"
          target="_blank"
          class="text-white/50 hover:text-white/80 cursor-pointer"
          >Oy ve Ötesi</a
        >
        <span class="text-white/50 ml-3 mr-3 select-none">x</span>
        <a
          href="https://sts.chp.org.tr/"
          target="_blank"
          class="text-white/50 hover:text-white/80 cursor-pointer"
          >CHP / YSK</a
        >
      </div>
    </div>
  </div>
</template>

<style scoped>
input {
  border: none;
  padding: 0;
  width: 235px;
  background: repeating-linear-gradient(
      90deg,
      dimgrey 0,
      dimgrey 1ch,
      transparent 0,
      transparent 1.5ch
    )
    0 100%/16ch 3px no-repeat;
  letter-spacing: 0.5ch;
  font: 3ch droid sans mono, consolas, monospace;
  color: dodgerblue;
}
input:focus {
  outline: none;
  color: rgb(151, 196, 241);
}
</style>
