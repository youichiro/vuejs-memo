<template>
  <div id="main">
    <h1 class="main__title">メモアプリSPA版</h1>
    <div class="main__mode">{{ editMode ? '編集' : '一覧' }}</div>
    <div class="main__columns">
      <div class="main__show">
        <ul class="memo__items">
          <li v-for="memo in memos" v-bind:key="memo.id" class="memo__item">
            <div v-on:click="changeEditMode(memo.id)" class="memo__link">
              {{ memo.title || 'null' }}
            </div>
          </li>
        </ul>
        <div class="memo__btn">
          <button v-on:click="add()" class="add-btn">＋</button>
        </div>
      </div>
      <div class="main__edit" v-bind:class="{ hidden: !editMode }">
        <form class="edit__form">
          <textarea v-model="form.text" :placeholder="getPlaceholder()" class="textarea"></textarea>
        </form>
        <div class="edit__btns">
          <button type="submit" v-on:click="save()" class="save-btn">編集</button>
          <button type="submit" v-on:click="del()" class="del-btn">削除</button>
          <button type="submit" v-on:click="cancel()" class="cancel-btn">キャンセル</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const STORAGE_KEY = 'vue-cli-memo'
const memoStrage = {
  fetch: () => {
    const memos = JSON.parse(
      localStorage.getItem(STORAGE_KEY) || '[]'
    )
    memos.forEach((memo, index) => {
      memo.id = index
    })
    memoStrage.uid = memos.length
    return memos
  },
  save: (memos) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(memos))
  }
}

export default {
  name: 'memo',
  data () {
    return {
      memos: [],
      form: { text: '' },
      editMode: false,
      currentMemo: null,
      placeholder: ''
    }
  },
  created () {
    this.memos = memoStrage.fetch()
  },
  watch: {
    memos: {
      handler: (memos) => memoStrage.save(memos),
      deep: true
    }
  },
  methods: {
    add: function () {
      const newMemo = { id: memoStrage.uid++, title: '新規メモ', text: '' }
      this.memos.push(newMemo)
    },
    changeEditMode: function (id) {
      this.editMode = true
      this.currentMemo = this.memos.find(memo => memo.id === id)
    },
    cancel: function () {
      this.editMode = false
    },
    save: function () {
      const lines = this.form.text.split('\n')
      const title = lines[0]
      const text = lines.slice(1).join('\n')
      this.currentMemo.title = title
      this.currentMemo.text = text
      this.editMode = false
      this.currentMemo = null
      this.form.text = ''
    },
    del: function () {
      const id = this.currentMemo.id
      this.memos = this.memos.filter(memo => memo.id !== id)
      this.editMode = false
    },
    getPlaceholder: function () {
      if (this.currentMemo) {
        return `title: ${this.currentMemo.title}\ntext: ${this.currentMemo.text}`
      } else {
        return 'title:\ntext:'
      }
    }
  }
}
</script>

<style lang="scss">
  @import "../assets/stylesheets/base.scss";
  @import "../assets/stylesheets/main.scss";
  @import "../assets/stylesheets/edit.scss";
  @import "../assets/stylesheets/atoms.scss";
</style>
