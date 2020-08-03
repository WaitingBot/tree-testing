<template>
  <div class="input">
    <p>Введите количество игроков</p>
    <input class="input__box" type="text" placeholder="input" v-model="number" @input="changeNumber(number)">
  </div>
</template>

<script>
export default {
  name: 'InputPlayers',
  data() {
    return {
      number: 8
    }
  },
  
  methods: {
    changeNumber(newNumber) {
      if(/^-?[\d.]+(?:e-?\d+)?$/.test(newNumber)) {
        this.$emit('newPlayersNumber', newNumber)
      }
    }
  },
  
  mounted() {
    this.changeNumber(8)
  }
  
}

</script>

<style scoped>

.input {
  font-size: 18px;
}

p, input {
  display: inline-block;
}

.input__box {
  margin-left: 10px;
  padding: 5px 5px;
}

</style>