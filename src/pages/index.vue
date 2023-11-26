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
  <div>
    <div text-4xl>
      <div i-whh-pastealt inline-block />
    </div>
    <p>
      <a rel="noreferrer" href="https://github.com/Llldmiao/supercut" target="_blank">
        在线剪切板
      </a>
    </p>

    <div py-4 />

    <TheInput
      v-model="name"
      placeholder="粘贴板名称"
      autocomplete="false"
      @keydown.enter="go"
    />

    <div>
      <button

        :disabled="!name"
        m-3 cursor-pointer text-sm btn
        @click="go"
      >
        {{ t('button.go') }}
      </button>
    </div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
