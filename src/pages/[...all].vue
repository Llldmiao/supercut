<script setup lang="ts">
import axios from 'axios'
import copy from 'copy-to-clipboard'
import { ElMessage } from 'element-plus'

// import { DocumentCopy } from '@element-plus/icons-vue'
import { CopyOutline } from '@vicons/ionicons5'
import { NIcon } from 'naive-ui'

const API = {
  queryDetails: '/clipboard/queryDetails',
  saveText: '/clipboard/saveText',
  uploadFile: '/clipboard/uploadFile',
  deleteFile: '/clipboard/deleteFile',
  downloadFile: '/clipboard/downloadFile',
}

// const props = defineProps<{ name: string }>()
const route = useRoute()
const clipboardName = ref(route.params.all[0])

const textareaContent = ref('')

function copyToClipboard() {
  copy(textareaContent.value, {
    debug: true,
    message: 'Copy to clipboard',
    format: 'text/html',
  })
  ElMessage({
    message: '已复制~~',
    type: 'success',
  })
}

async function saveText() {
  try {
    const res = await axios.post(API.saveText, {
      clipboardName: clipboardName.value,
      clipboardText: textareaContent.value,
    })
    return res
  }
  catch (error) {
    console.error(error)
  }
}
const saveTextInterval = ref<NodeJS.Timer | null>(null)

const TAB = [{
  label: 'text',
  name: '剪切板文本',
}, {
  label: 'file',
  name: '文件',
}]
const currentTab = ref('text')
function onTabClick(label: string) {
  // console.log('onTabClick', label)
  currentTab.value = label
}

const createTime = ref('')
const lastModifiedTime = ref('')
onMounted(() => {
  axios.get(API.queryDetails, { params: { clipboardName: clipboardName.value } }).then((res) => {
    // console.log('res: ', res.data.data)
    textareaContent.value = res.data.data.clipboardText
    createTime.value = res.data.data.gmtCreate
    lastModifiedTime.value = res.data.data.gmtModify
  })
  setTimeout(() => {
    // console.log('headerR', headerR);

  }, 100)
})

document.addEventListener('keydown', (e) => {
  if ((window.navigator.userAgent.match('Mac') ? e.metaKey : e.ctrlKey) && (e.key === 's' || e.key === 'S')) {
    e.preventDefault()
    saveText()
  }
}, false)
let timeoutId: NodeJS.Timeout | null = null

function handleInput() {
  if (timeoutId)
    clearTimeout(timeoutId)

  timeoutId = setTimeout(saveText, 1000)
}

const headerR = ref('')

function onaddfile(err: any, file: any) {
  console.warn('addfile', err, file)
}

onBeforeUnmount(() => {
  saveTextInterval.value && clearInterval(saveTextInterval.value)
})
</script>

<template>
  <div ml-a mr-a h-full max-w-7xl p-b-6>
    <div inline-block flex text-4xl class="header">
      <div i-whh-pastealt w-4 />
      <p ref="headerR" ml-2 font-size-4>
        粘贴板名称：{{ route.params.all[0] }}
      </p>
    </div>
    <div class="content" mt-5 flex-row justify-center>
      <div class="content-left" wa flex-auto flex-row>
        <div class="tabs" flex>
          <div
            v-for="item in TAB" :key="item.label" class="tab" :class="[item.label === currentTab ? 'active' : '']"
            cursor-pointer p="x-5 y-3" @click="onTabClick(item.label)"
          >
            {{ item.name }}
          </div>
        </div>
        <div v-if="currentTab === 'text'" class="note-area" ha w-full>
          <textarea
            id="note" v-model="textareaContent" class="custom-scrollbar" name="note" w-full resize-none border
            border-coolgray outline-none placeholder="可以随便记录点什么，单次支持20万字符..." @input="handleInput"
          />

          <div class="content-right" flex-auto>
            <n-button color="green" @click="copyToClipboard">
              <template #icon>
                <NIcon>
                  <CopyOutline />
                </NIcon>
              </template>
              复制文本
            </n-button>
          </div>
        </div>
        <div v-if="currentTab === 'file'" class="note-area" min-h-lg w-full border>
          <span>最多上传10个文件，单文件50MB内，总大小500MB内， 当前已传 0 个文件，共计 10.1KB，还能上传 10 个文件，有效期为上传后7天内。
            如需大文件传送，可以使用 即时传，支持500M以内大小文件传送。</span>
          <FileOpen id="filepond" @addfile="onaddfile" />
        </div>
      </div>
    </div>
  </div>
</template>

<style type="less" scoped>
button {
  background-color: green;
}

.tab {
  &.active {
    border: 1px solid rgb(255, 255, 255);
    background: rgb(238, 238, 238);
  }
}

.note-area:has(#filepond) {
  border: none;
}

#note {
  min-height: 50vh;
  font-family: Consolas, "Liberation Mono", Courier, monospace;
  line-height: 1.4;
  font-size: 16px;
  padding: 5px;
  background: transparent;
  white-space: pre-wrap;
  word-wrap: break-word;
  word-spacing: normal;

  &:focus {
    box-shadow: 0 3px 10px rgba(0, 0, 255, 0.3);
  }
}

#filepond {
  margin: auto;
  width: 50%;
}
</style>
