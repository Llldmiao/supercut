<script setup lang="ts">
defineOptions({
  name: 'IndexPage',
})
const user = useUserStore()
const name = ref(user.savedName)

const router = useRouter()
function go() {
  if (name.value)
    router.push(`/${encodeURIComponent(name.value)}`)
}

const { t } = useI18n()
</script>

<template>
  <div min-h-screen flex flex-col items-center justify-center>
    <div flex items-center text-4xl space-x-3>
      <div i-whh-pastealt inline-block />
      <a rel="noreferrer" href="https://github.com/Llldmiao/supercut" target="_blank">
        在线剪切板
      </a>
    </div>
    <div my-8 text-xl text-slate-500>
      请输入剪切板名称并按enter进入
    </div>
    <div mt-8 max-w-md w-full px-10>
      <div flex items-center space-x-2>
        <n-input id="input" v-model:value="name" type="text" placeholder="粘贴板名称" flex-1 @keydown.enter="go" />
        <n-button class="bg-[#2f2f31]" color="#2f2f31" @click="go">
          {{ t('button.go') }}
        </n-button>
      </div>
    </div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
