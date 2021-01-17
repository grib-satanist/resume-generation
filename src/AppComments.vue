<template>
  <div class="container">
    <p v-if="comments.length === 0">
      <button class="btn primary" @click="$emit('load')">Загрузить комментарии</button>
    </p>
    <div class="card" v-else>
      <h2>Комментарии</h2>
      <ul class="list">
        <li v-for="item in comments" :key="item" class="list-item">
          <div>
            <p><strong>{{item.email}}</strong></p>
            <small>{{item.text}}</small>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
export default {
  emits: ['load'],
  props: ['comments']
}
</script>
<style lang="">
  
</style>