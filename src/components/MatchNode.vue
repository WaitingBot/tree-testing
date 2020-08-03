<template>
    
    <div class="item">
      
      <div class="item-parent">
        <p class="match-view">{{ matches.name }}</p>
      </div>
      <div class="item-childrens" v-if="matches.children && matches.children.length">
        <div class="item-child"
             v-for="child in matches.children">
            <MatchNode :matches="child"
            />
        </div>
      </div>
      
      <div v-else class="item-childrens">
        <div class="item-child"
             v-for="player in matches.players"
             :key="player.id"
             @mousedown="drag($event, player)"
             @mouseenter="drop($event, player)"
        >
          <p class="match-view">{{ player.name }}</p>
        </div>
      </div>
      
    </div>
    
</template>

<script>
import InputPlayers from './InputPlayers';

export default {
  name: "MatchNode",
  props: {
    matches: Object
  },
  components: {
    InputPlayers
  },
  data() {
    return {
      dragged: false,
      winner: {
        name: '',
        id: null
      }
    }
  },
  
  methods: {
    drag(event, player) {
      this.$root.$emit('dragMe', {event, player})
      this.dragged = true
      
    },
    
    drop(event, player) {
      this.$root.$emit('afterDrop', {event, player})
      this.dragged = false
    },
  }
  
}
</script>

<style lang="scss">

$side-margin: 50px;
$vertical-margin: 10px;
$color-lines: #9778D3;

.dragged-item p{
  opacity: 0.5;
}

.match-view {
  padding: 15px;
  margin: 0;
  width: 100px;
  height: 18px;
  border: 1px solid #5F8362;
  user-select: none;
  cursor: pointer;
  background-color: #fff;
}

.item{
  display: flex;
  flex-direction: row-reverse;
  
  &-parent{
    position: relative;
    margin-left: $side-margin;
    display: flex;
    align-items: center;
    &:after{
      position: absolute;
      content: '';
      width: $side-margin/2;
      height: 2px;
      left: 0;
      top: 50%;
      background-color: $color-lines;
      transform: translateX(-100%);
    }
  }
  &-childrens{
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  &-child{
    display: flex;
    align-items: flex-start;
    justify-content: flex-end;
    margin-top: $vertical-margin;
    margin-bottom: $vertical-margin;
    position: relative;
    &:last-child{
      &:after{
        transform: translateY(-100%);
      }
    }
    &:only-child:after{
      display: none;
    }
  }
}

.item .item-child {
  &:before{
    content: '';
    position: absolute;
    background-color: $color-lines;
    right: 0;
    top: 50%;
    transform: translateX(100%);
    width: 25px;
    height: 2px;
  }
  &:after{
    content: '';
    position: absolute;
    background-color: $color-lines;
    right: -$side-margin / 2;
    height: calc(50% + 22px) ;
    width: 2px;
    top: 50%;
  }
}


</style>