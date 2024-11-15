<script setup lang="ts">
import { onMounted, reactive } from 'vue'

const articles = reactive<Article[]>([])
const key_articles = `articles`
const key___editor_content = `__editor_content`
const key___editor_content_id = `__editor_content_id`

const storage = (window as any).localforage.config({ size: 2 ** 32 })

interface Article {
  id: string
  title: string
  date: number
  update: number
}

const articlemap: { [x: string]: Article } = {}

onMounted(async () => {
  const data = (await storage.getItem(key_articles)) || []
  articles.length = 0
  articles.push(...data)
  for (let i = 0; i < data.length; i++) {
    const v = data[i]
    articlemap[v.id ?? ``] = v
  }
})

async function hanldeClick(key: string) {
  const content = await storage.getItem(key) ?? ``
  const current = localStorage.getItem(key___editor_content) ?? ``
  const currentid = (await storage.getItem(key___editor_content_id)) ?? `${Date.now()}${Math.random()}`
  await storage.setItem(currentid, current)
  if (articlemap[currentid]) {
    articlemap[currentid].title = current.trimStart().slice(0, 30)
  }
  else {
    articles.push({
      id: currentid,
      title: current.trimStart().slice(0, 30),
      date: Date.now(),
      update: Date.now(),
    })
  }

  await storage.setItem(key_articles, articles)
  localStorage.setItem(key___editor_content, content)
  await storage.setItem(key___editor_content_id, key)
  location.reload()
}

async function newArticle() {
  const current = localStorage.getItem(key___editor_content) ?? ``
  const currentid = (await storage.getItem(key___editor_content_id)) ?? `${Date.now()}${Math.random()}`
  await storage.setItem(currentid, current)
  if (articlemap[currentid]) {
    articlemap[currentid].title = current.trimStart().slice(0, 30)
  }
  else {
    articles.push({
      id: currentid,
      title: current.trimStart().slice(0, 30),
      date: Date.now(),
      update: Date.now(),
    })
  }
  await storage.setItem(key_articles, JSON.stringify(articles))
  localStorage.setItem(key___editor_content, `## 新文章`)
  location.reload()
}
</script>

<template>
  <MenubarMenu>
    <MenubarTrigger>
      历史文章
    </MenubarTrigger>
    <MenubarContent align="start">
      <MenubarItem @click="newArticle()">
        <el-icon class="mr-2 h-4 w-4" />
        <span>新文章</span>
      </MenubarItem>
      <MenubarItem v-for="(item) in articles" :key="item.id" style="max-width: 320px;text-overflow: hidden" @click="hanldeClick(item.id)">
        <el-icon class="mr-2 h-4 w-4" />
        <span>{{ item.title }} {{ item.date }}</span>
      </MenubarItem>
    </MenubarContent>
  </MenubarMenu>
</template>
