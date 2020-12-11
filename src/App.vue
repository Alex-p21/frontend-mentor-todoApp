<template>

<div class="container">
  <header class="header">
    <div class="header__container">
      <h1 class="header__title">TODO</h1>
      <div class="header__modeNuit" @click="toggleTheme"></div>
    </div>
  </header>
  
  <section class="todo">
    <div class="todo__submit">
      <div class="checkbox" :class="{active: submitChecked}" @click="allCheck">
        <span class="checkbox__radio"></span>
      </div>
      <input 
        class="todo__submit__input" 
        type="text" 
        maxlength="90"
        placeholder="Create a new todo..." 
        v-model="newTodo"
        @keyup.enter="addTodo"
      />
    </div>

    <draggable 
      class="todo__list"
      :list="filteredTodos"
      v-bind="dragOptions"
      @start="drag = true"
      @end="drag = false"
      item-key="order"
    >
      <template #item="{element, index}">
        <div class="todo__list__card" :class="element.isActive ? 'active':''">
          <div class="checkbox" @click="element.isActive = !element.isActive">
            <span class="checkbox__radio"></span>
          </div>
          <p class="checkbox__text" @dblclick.prevent="modifyTodo(element)"> {{ element.txt }} </p>
          <input 
            type="text" 
            class="checkbox__focus" 
            :class="{active: element === editing}" 
            v-model="element.txt" 
            @keyup.enter="doneEdit" 
            @blur="doneEdit"
          >
          <div class="checkbox__close" @click="this.todos.splice(index, 1)"></div>
        </div>
      </template>
    </draggable>

    <div class="todo__options__desktop">
        <p class="todo__options__desktop__itemLeft"> {{itemLeft}} item left</p>
        <div class="todo__options__desktop__select">
          <p class="todo__options__desktop__select__all" :class="{active:filter === 'selectAll'}" @click.prevent="filter = 'selectAll'">All</p>
          <p class="todo__options__desktop__select__active" :class="{active:filter === 'selectActive'}" @click.prevent="filter = 'selectActive'">Active</p>
          <p class="todo__options__desktop__select__completed" :class="{active:filter === 'selectCompleted'}" @click.prevent="filter = 'selectCompleted'">Completed</p>
        </div>
        <p class="todo__options__desktop__clear" @click.prevent="clearCompleted">Clear Completed</p>
    </div>

    <div class="todo__options__phone">
      <p class="todo__options__phone__all" :class="{active:filter === 'selectAll'}" @click.prevent="filter = 'selectAll'">All</p>
      <p class="todo__options__phone__active" :class="{active:filter === 'selectActive'}" @click.prevent="filter = 'selectActive'">Active</p>
      <p class="todo__options__phone__completed" :class="{active:filter === 'selectCompleted'}" @click.prevent="filter = 'selectCompleted'">Completed</p>
    </div>
    <p class="todo__info">Drag and drog to reorder list</p>
  </section>
</div>

</template>

<script>
import draggable from "vuedraggable";

export default {
  name: 'App',
  components: {
    draggable
  },

  data () {
    return {
      filter: 'selectAll',
      newTodo: '',
      submitChecked: false,
      editing: null,
      enabled: true,
      dragging: false, 
      todos: [
        { txt: 'Complete online JavaScript course', isActive: true },
        { txt: 'Jog around the park 3x', isActive: false },
        { txt: '10 minutes meditation', isActive: false },
        { txt: 'Read for 1 hour', isActive: false },
        { txt: 'Pick up groceries', isActive: false },
        { txt: 'Complete Todo App on Frontend Mentor', isActive: true }
      ]
    }
  }, 

   methods: {
     addTodo() {
            if (this.newTodo != '') {
                this.todos.push({
                txt: this.newTodo,
                isActive: false
                })
                this.newTodo = ''
            } else {
                return
            }
        },
    clearCompleted() {
        let index = this.todos.length - 1
        while (index >= 0) {
            if (this.todos[index].isActive === true) {
            this.todos.splice(index, 1);
            }
            index -= 1;
        }
    },
    allCheck() {
        if (this.submitChecked === true) {
            this.todos.forEach (item => {
            item.isActive = false
            })  
            this.submitChecked = false
        } else {
            this.todos.forEach (item => {
            item.isActive = true
            })
            this.submitChecked = true
        }
    },
    toggleTheme() {
      if (this.theme == "dark") {
				this.theme = "light";
				document.body.setAttribute("data-theme", "light");
				localStorage.setItem("theme", "light");
			} else {
				this.theme = "dark";
				document.body.setAttribute("data-theme", "dark");
				localStorage.setItem("theme", "dark");
			} 
    }, 
    modifyTodo(todo) { this.editing = todo },
    doneEdit() { this.editing = null },
  },

  computed: {
    selectCompleted() { return this.todos.filter( item => item.isActive === true) },    
    itemLeft() { return this.todos.filter( item => item.isActive === false).length }, 
    filteredTodos() {
      if (this.filter === 'selectActive') {
        return this.todos.filter( item => item.isActive === false)
      } else if (this.filter === 'selectCompleted') {
        return this.todos.filter( item => item.isActive === true)
      } else {
        return this.todos
      }
    },
    dragOptions() {
      return {
        animation: 200,
        disabled: false,
        ghostClass: "ghost"
      };
    }
  }
}
</script>

<style lang="scss">
@import './scss/tools/variables.scss';
@import './scss/modules/header.scss';
@import './scss/modules/todo.scss';
@import './scss/modules/checkbox.scss'; 

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  user-select: none;
}

html, body {
  font-family: 'Josefin Sans', sans-serif;
  font-size: 20px;
  color: var(--TextColor);
  background: var(--HtmlBackground);
  overflow: hidden;

  @media (max-width: 700px) {
    font-size: 16px;
  }
}

#app {
  width: 100vw;
  height: 100vh;

  display: flex;
  justify-content: center;

  @media (max-width: 700px) { height: 730px; }
}

.container {
  flex-basis: $Desktop;
  background-color: var(--ContainerColor);

  @media (max-width: 700px) { flex-basis: $Mobile; }
}
</style>
