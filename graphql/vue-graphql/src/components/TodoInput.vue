<template>
  <div class="formInput">
    <input
      class="input"
      placeholder="What needs to be done?"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <i class="downArrow fa fa-angle-right" />
  </div>
</template>

<script>
import gql from 'graphql-tag'
import { GET_MY_TODOS } from './TodoPrivateList.vue'

const ADD_TODO = gql`
  mutation insert_todos($todo: String!, $isPublic: Boolean!){
    inserttodos(objects: {title: $todo, is_publick: $isPublic}){
      affected_rows
      returning {
        id
        title
        is_completed
        created_at
        is_public
      }
    }
  }
`
  export default {
    props: ['type'],
    data() {
      return {
        newTodo: '',
      }
    },
    methods: {
      addTodo: function () {
        const title = this.newTodo && this.newTodo.trim()
        const isPublic = this.type === 'public'
        this.$apollo.mutate({
          mutation: ADD_TODO,
          variables: {
            todo: title,
            isPublic: isPublic
          },
          update: (cache, { data: { insert_todos } }) => {
            // eslint-disable-next-line
            console.log(insert_todos)
            try {
              if (this.type === 'private') {
                const data = cache.readQuery({
                  query: GET_MY_TODOS
                })
                const insertedTodo = insert_todos.returning
                data.todos.splice(0, 0, insertedTodo[0])
                cache.writeQuery({
                  query: GET_MY_TODOS,
                  data
                })
              }
            } catch (error) {
              // eslint-disable-next-line
              console.log(error)
            }
          }
        })
        this.newTodo = ''
      },
    }
  }
</script>
