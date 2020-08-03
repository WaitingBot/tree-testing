<template>
  <div class="input">
    <p>Введите номер победившего игрока</p>
    <input class="input__box" type="text" placeholder="ввод" v-model="number" @keyup.enter="newWinner(number)">
  </div>
</template>

<script>
export default {
  name: "InputWinPlayer",
  data() {
    return {
      number: null
    }
  },
  
  methods: {
    newWinner(id) {
      this.$emit('newWinner', id)
    }
  }
}
</script>

<style scoped>

</style>