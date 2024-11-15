<script setup lang="ts">
import { onMounted } from 'vue'
// id = ""+Date.now()+Math.random()
// title = ""
// date = ""
// update = ""

interface Article {
  id: string
  title: string
  date: number
  update: number
}

const articlemap: { [x: string]: Article } = {}
const articles: Article[] = JSON.parse(localStorage.getItem(`articles`) || `[]`)

onMounted(() => {
  for (let i = 0; i < articles.length; i++) {
    const v = articles[i]
    articlemap[v.id ?? ``] = v
  }
})

function hanldeClick(key: string) {
  const content = localStorage.getItem(key) ?? ``
  const current = localStorage.getItem(`__editor_content`) ?? ``
  const currentid = localStorage.getItem(`__editor_content_id`) ?? `${Date.now()}${Math.random()}`
  localStorage.setItem(currentid, current)
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

  localStorage.setItem(`articles`, JSON.stringify(articles))
  localStorage.setItem(`__editor_content`, content)
  localStorage.setItem(`__editor_content_id`, key)
  location.reload()
}

function newArticle() {
  const current = localStorage.getItem(`__editor_content`) ?? ``
  const currentid = localStorage.getItem(`__editor_content_id`) ?? `${Date.now()}${Math.random()}`
  localStorage.setItem(currentid, current)
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
  localStorage.setItem(`articles`, JSON.stringify(articles))
  localStorage.removeItem(`__editor_content`)
  localStorage.setItem(`__editor_content`, `## 新文章`)
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
