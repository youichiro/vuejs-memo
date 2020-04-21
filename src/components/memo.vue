<template>
  <div id="main">
    <h1 class="main__title">メモアプリSPA版</h1>
    <div class="main__mode">{{ editMode ? '編集' : '一覧' }}</div>
    <div class="main__columns">
      <div class="main__show">
        <div class="show__body">
          <ul class="memo__items">
            <li v-for="memo in memos" v-bind:key="memo.id" class="memo__item">
              <div v-on:click="changeEditMode(memo.id)" class="memo__link">
                {{ memo.title || 'null' }}
              </div>
            </li>
          </ul>
          <div class="show__btn">
            <button v-on:click="add()" class="add-btn">＋</button>
          </div>
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
      form: {
        text: ''
      },
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
  $main: #45A1CF;
  @mixin btn {
    border-radius: 0.25rem;
    text-align: center;
    border: none;
    font-weight: bold;
    font-size: 0.5rem;
    text-decoration: none;
    color: white;
    transition: all .2s ease-out;
    &:focus {
        outline: 0;
    }
  }
  body {
    background-color:  #efefef;
  }
  #main {
    margin: auto;
    padding: 0.5rem 3rem;
    max-width: 70rem;
  }
  .hidden {
    display: none;
  }
  .main__columns {
    display: flex;
    flex-wrap: wrap;
  }
  .main__mode {
    color: gray;
  }
  .main__show {
    background-color: white;
    padding: 1rem 0.5rem;
    flex: 1;
    min-width: 24rem;
    margin: 0.25rem;
  }
  .memo__items {
    margin: 0;
  }
  .memo__link {
    text-decoration: none;
    cursor: pointer;
    transition: all .2s ease-out;
    &:hover {
      color: $main;
      text-decoration: underline;
    }
    font-size: 1.2rem;
  }
  .show__btn {
    margin: 1rem 0 1rem 20px;
  }
  .add-btn {
    @include btn;
    background-color: $main;
    &:hover {
      background-color: darken($main, 10);
    }
  }
  .main__edit {
    flex: 1;
    background-color: white;
    text-align: center;
    padding-bottom: 1rem;
    min-width: 24rem;
    margin: 0.25rem;
  }
  .edit__form {
    margin: 1rem;
  }
  .textarea {
    padding: 0.5rem;
    width: 90%;
    height: 16rem;
    line-height: 1.5rem;
    font-size: 1rem;
    border-radius: 0.5rem;
    border: solid 1px rgba(gray, 0.5);
    &:focus {
        outline: 0;
    }
  }
  .edit__btns {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 2rem;
  }
  .save-btn {
    flex: 1;
    @include btn;
    background-color: #A4C520;
    font-size: 0.75rem;
    padding: 0.1rem 1rem;
    margin-right: 0.5rem;
    &:hover {
      background-color: darken(#A4C520, 10);
    }
  }
  .del-btn {
    flex: 1;
    @include btn;
    background-color: #DA6272;
    font-size: 0.75rem;
    padding: 0.1rem 1rem;
    margin-right: 0.5rem;
    &:hover {
      background-color: darken(#DA6272, 10);
    }
  }
  .cancel-btn {
    flex: 1;
    @include btn;
    background-color: #a9a9a9;
    font-size: 0.75rem;
    padding: 0.1rem 1rem;
    &:hover {
      background-color: darken(#a9a9a9, 10);
    }
  }
</style>
