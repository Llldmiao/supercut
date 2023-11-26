<script setup lang="ts">
import axios from 'axios'

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
// console.log('router: ', route.params.all[0])
// watchEffect(() => {
//   user.setNewName(props.name)
// })
const textareaContent = ref('')

function handleKeyDown(event) {
  // 检查是否按下了 Ctrl 键，并且同时按下了 S 键
  if (event.ctrlKey && event.key === 's') {
    // 阻止浏览器默认行为（避免保存页面）
    event.preventDefault()

    // 输出 textarea 中的内容
    axios.post(API.saveText, {
      clipboardName: clipboardName.value,
      clipboardText: textareaContent.value,
    }).then(() => {
      // TODO: 提示成功/失败
      // console.log(res)
    })
  }
}

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
function onFileSelected() {
  // TODO: 待完善
  // console.log('onFileSelected', e)
}

onMounted(() => {
  axios.get(API.queryDetails, { params: { clipboardName: clipboardName.value } }).then((res) => {
    // console.log('res: ', res.data.data)
    textareaContent.value = res.data.data.clipboardText
  })
})
</script>

<template>
  <div>
    <div inline-block flex text-4xl class="header">
      <div i-whh-pastealt w-4 />
      <p ml-2 font-size-4>
        粘贴板名称：{{ route.params.all[0] }}
      </p>
    </div>
    <div class="content" mt-5 flex justify-center>
      <div class="content-left" wa flex-row>
        <div class="tabs" flex>
          <div
            v-for="item in TAB" :key="item.label" class="tab" :class="[item.label === currentTab ? 'active' : '']"
            cursor-pointer p="x-5 y-3" @click="onTabClick(item.label)"
          >
            {{ item.name }}
          </div>
        </div>
        <div v-if="currentTab === 'text'" class="note-area" ha wa>
          <textarea id="note" v-model="textareaContent" name="note" min-h-lg w-3xl resize-none border placeholder="可以随便记录点什么，单次支持20万字符..." @keydown="handleKeyDown" />
        </div>
        <div v-if="currentTab === 'file'" class="note-area" min-h-lg w-3xl border>
          <span>最多上传10个文件，单文件50MB内，总大小500MB内， 当前已传 0 个文件，共计 10.1KB，还能上传 10 个文件，有效期为上传后7天内。
            如需大文件传送，可以使用 即时传，支持500M以内大小文件传送。</span>
          <input type="file" multiple @input="onFileSelected">
        </div>
        <div class="content-right" w-md>
          <button m-3 text-sm btn>
            复制文本
          </button>
          <button m-3 text-sm btn>
            下载保存
          </button>
          <div class="info">
            创建时间：
            2023-09-16 20:20:26
            更新时间：
            2023-11-04 10:27:20
            上次查看：
            2023-11-04 10:27:02
            过期删除时间：(三年后)
            2026-11-03 10:27:02
            查看次数：
            105次
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style type="less" scoped>
.tab {
  &.active {
    border: 1px solid rgb(255, 255, 255);
    background: rgb(238, 238, 238);
  }
}

#note {
  min-height: 720px;
  font-family: Consolas, "Liberation Mono", Courier, monospace;
  line-height: 1.4;
  font-size: 16px;
  padding: 5px;
  background: transparent;
  white-space: pre-wrap;
  word-wrap: break-word;
  word-spacing: normal;

  &:focus {
    border: none;
  }
}
</style>
