<template>
  <div id="container">
    <div id="todo">
      <form @submit.prevent>
        <textarea
          placeholder="TODOを入力しましょう！"
          v-model="newReport"
          @keydown.ctrl.enter="addTodo(newReport)"
        ></textarea>
        <button id="submit">投稿</button>
      </form>
      <p id="errMsg" v-if="valiMsg !== ''">{{ valiMsg }}</p>
      <div id="todoContent">
        <div v-for="(item, index) in items" :key="index" class="todoRecord">
          <label v-bind:class="{ done: item.isChecked }" class="todoText">
            <input type="checkbox" v-model="item.isChecked" @click="pushSave(index)" />
            {{ item.title }}
          </label>
          <button class="todoDelBtn" @click="deleteText(index)" tabindex="-1">削除</button>
        </div>
      </div>
    </div>

    <div id="dialyReport">
      <button id="copyBtn" @click="writeToClipboard()">コピー</button>
      <br />

      <div id="target" ref="target">
        <strong>今日やったこと</strong>
        <br />
        <span v-for="(item, index) in items" :key="index">
          <span v-if="item.isChecked">
            ・{{ item.title }}
            <br />
          </span>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'todo',
  data() {
    return {
      items: [],
      newReport: '',
      valiMsg: ''
    }
  },
  methods: {
    //methodsオプションをまるっと追加
    addTodo: text => {
      if (this.newReport === '') {
        this.valiMsg = 'テキストが空です。'
        return
      }
      this.valiMsg = ''

      this.items.push({
        title: text,
        isChecked: false
      })
      this.newReport = ''
      this.saveTodo()
    },
    saveTodo: () => {
      localStorage.setItem('items', JSON.stringify(this.items))

      // 投稿日付保存
      localStorage.setItem('reportDate')
    },
    loadTodo: () => {
      this.items = JSON.parse(localStorage.getItem('items'))
      if (!this.items) {
        this.items = []
      }
    },

    // 分報単体削除
    deleteText: index => {
      this.items.splice(index, 1)
      this.saveTodo()
    },

    // チェックボックス押下時localstorageに保存
    pushSave: index => {
      // v-modelの切り替え反応よりもv-on:clickの方が発動タイミングが早い
      let item = this.items[index]
      item.isChecked = !item.isChecked
      this.items.splice(index, 1, item)
      this.saveTodo()
    },

    writeToClipboard: () => {
      let elem = this.$refs.target

      let range = document.createRange()
      range.selectNode(elem)

      let selection = getSelection()
      selection.removeAllRanges()
      selection.addRange(range)

      document.execCommand('copy')
      selection.removeAllRanges()
    }
  },
  mounted: () => {
    this.loadTodo()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.done {
  text-decoration: line-through;
}
form {
  display: inline-flex;
  width: 90%;
  margin-bottom: 1.25rem;
}
textarea {
  resize: none;
  width: 80%;
  height: 3rem;
}
#submit {
  width: 20%;
}
#errMsg {
  color: red;
}
#container {
  width: 100%;
}
#todo {
  float: left;
  width: 50%;
}
.todoContent {
  width: 100%;
}
.todoRecord {
  position: relative;
  width: 100%;
}
.todoText {
  position: relative;
  left: 0px;
}
.todoDelBtn {
  display: block;
  float: right;
  margin-right: 10%;
}
#target {
  width: 50%;
  float: left;
}
#copyBtn {
  width: 50%;
  height: 3.25rem;
  margin-top: 0.25rem;
  margin-bottom: 20px;
}
</style>
