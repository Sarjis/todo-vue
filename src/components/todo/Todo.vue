<template>
    <div class="container">
        <SearchForm @addTodo="addTodo" @search="search"/>
        <div class="row">
            <div class="col-md-6 offset-3">
                <TodoList :filteredTodos="filteredTodos"></TodoList>
                <p>{{remainingTodo}} {{remainingTodo===1?"Todo":'Todos'}} Left</p>
            </div>
            <div class="col-md-6 offset-3 my-3">
                <button class="btn btn-info mx-1" @click="visibility='all'">All</button>
                <button class="btn btn-primary mx-1" @click="visibility='active'">Active</button>
                <button class="btn btn-success mx-1" @click="visibility='completed'">Completed</button>
            </div>
        </div>
    </div>
</template>

<script>
    import SearchForm from "./SearchForm";
    import TodoList from "./TodoList";

    export default {
        name: "Todo",
        data() {
            return {
                todos: [],
                totalTodo: 0,
                visibility: 'all',
            }
        },
        components: {
            SearchForm,
            TodoList
        },
        methods: {
            addTodo(newText) {
                console.log(newText)
                this.todos.push({
                    text: newText,
                    completed: false,
                    id: new Date().getUTCMilliseconds() + '' + new Date().getUTCFullYear() + '' + new Date().getSeconds()
                })
                localStorage.setItem('localTodo', JSON.stringify(this.todos))

                newText = ''
            },
            removeTodo(i) {
                if (confirm('Are you sure?')) {
                    this.todos.splice(i, 1)
                    localStorage.setItem('localTodo', JSON.stringify(this.todos))
                }
            },
            search(keyword) {
                console.log(keyword)
                if (keyword) {
                    this.todos = this.todos.filter(todo => {
                            return todo.text.toLowerCase().includes(keyword.toLowerCase())
                            // return todo.text.toLowerCase().indexOf(this.searchText.toLowerCase()) !== -1
                        }
                    )
                } else {
                    this.todos = this.$store.getters.getLocalTodos
                }

            }
        },
        computed: {
            remainingTodo() {
                let count = 0;
                this.todos.forEach((todo) => {
                    let i = 1;
                    if (todo.completed === false)
                        count += i
                });
                return count
            },
            filteredTodos() {
                if (this.visibility === 'all') {
                    return this.todos
                } else if (this.visibility === 'active') {
                    return this.todos.filter(todo => !todo.completed)
                } else {
                    return this.todos.filter(todo => todo.completed)
                }
            },
        },
        created() {
            this.$store.dispatch('getLocalStorageData').then(() => {
                this.todos = this.$store.getters.getLocalTodos
            })
        },

    }
</script>

<style scoped>
</style>