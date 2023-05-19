<script setup lang="ts">
import { debug } from "console";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

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

const tcKimlikNo = ref("");
let chpData = ref(undefined);
const loading = ref(false);

async function sorgula() {
  if (!validateTckn()) return;
  loading.value = true;
  chpData.value = undefined;
  const { data, error } = await useAsyncData("chpFetch", () =>
    $fetch(`https://afg.theyka.net/chpfetch?tckn=${tcKimlikNo.value}`)
  );
  if (error.value) {
    toast.error("Bir hata oluştu. Lütfen tekrar deneyin.", {
      theme: "dark",
    });
  }
  if (data) {
    chpData.value = data.value as undefined;
  }
  tcKimlikNo.value = "";
  loading.value = false;
  debugger;
}

function validateTckn() {
  if (tcKimlikNo.value.length != 11) return false;
  const splittedTckn = tcKimlikNo.value.split("");
  const sumOfFirstTenDigits =
    splittedTckn
      .slice(0, 10)
      .map((digit) => parseInt(digit))
      .reduce((a, b) => a + b) % 10;

  if (parseInt(splittedTckn[10]) != sumOfFirstTenDigits) {
    toast.error("TC Kimlik Numarası hatalı.", { theme: "dark" });
    return false;
  }

  if (tcKimlikNo.value.length != 11) {
    toast.error("TC Kimlik Numarası 11 haneli olmalıdır.", { theme: "dark" });
    return false;
  }
  return true;
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
        <div class="flex justify-center items-center mt-8">
          <button
            type="button"
            @click="sorgula"
            class="h-22 w-32 flex justify-center items-center select-none border font-medium rounded-lg text-sm px-10 py-3 text-center mr-2 border-blue-500 text-blue-500"
            :class="
              tcKimlikNo.length != 11 || loading
                ? `opacity-50 cursor-not-allowed`
                : `hover:text-white hover:bg-blue-500 cursor-pointer`
            "
          >
            <div class="flex">
              <span
                v-if="!loading"
                class="flex justify-center items-center text-lg"
                >Sorgula</span
              >
              <svg
                v-if="loading"
                width="28"
                height="28"
                viewBox="0 0 38 38"
                xmlns="http://www.w3.org/2000/svg"
              >
                <defs>
                  <linearGradient
                    x1="8.042%"
                    y1="0%"
                    x2="65.682%"
                    y2="23.865%"
                    id="a"
                  >
                    <stop stop-color="#fff" stop-opacity="0" offset="0%" />
                    <stop
                      stop-color="#fff"
                      stop-opacity=".631"
                      offset="63.146%"
                    />
                    <stop stop-color="#fff" offset="100%" />
                  </linearGradient>
                </defs>
                <g fill="none" fill-rule="evenodd">
                  <g transform="translate(1 1)">
                    <path
                      d="M36 18c0-9.94-8.06-18-18-18"
                      id="Oval-2"
                      stroke="url(#a)"
                      stroke-width="2"
                    >
                      <animateTransform
                        attributeName="transform"
                        type="rotate"
                        from="0 18 18"
                        to="360 18 18"
                        dur="0.9s"
                        repeatCount="indefinite"
                      />
                    </path>
                    <circle fill="#fff" cx="36" cy="18" r="1">
                      <animateTransform
                        attributeName="transform"
                        type="rotate"
                        from="0 18 18"
                        to="360 18 18"
                        dur="0.9s"
                        repeatCount="indefinite"
                      />
                    </circle>
                  </g>
                </g>
              </svg>
            </div>
          </button>
        </div>
      </div>
      <div v-if="chpData" class="flex w-full justify-center">
        <Results :chpData="chpData" />
      </div>
      <div class="mt-5 flex flex-col items-center text-gray-300">
        <!--
        <div class="flex gap-1.5">
          <span> Toplamda </span>
          <span class="text-sky-400"> 31</span>
          <span> kişi sandığını kontrol etti</span>
        </div>
        -->
        <div class="select-none">
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
