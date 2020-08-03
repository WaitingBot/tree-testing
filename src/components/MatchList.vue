<template>
  <div class="wrapper"
       @mousemove="dragMove"
       @mouseup="dropped"
  >
    
    <MatchNode :matches="this.matches"
    />
    
    <div v-if="draggedItem">
      <div :style="draggedStyle" v-html="draggedItem"></div>
    </div>
  
  </div>
</template>

<script>
import MatchNode from './MatchNode';

export default {
  name: "MatchList",
  components: {
    MatchNode
  },
  props: {
    matches: Object
  },
  data() {
    return {
      draggedItem: null,
      draggedStyle: {},
      switchPlayers: {
        first: {},
        second: {}
      },
      isAfterDrop: false
    }
  },
  
  methods: {
    moveAt(e) {
      this.$set(this.draggedStyle, 'left', e.pageX - this.draggedStyle.offsetWidth / 2 + 'px')
      this.$set(this.draggedStyle, 'top', e.pageY - this.draggedStyle.offsetHeight / 2 + 'px')
    },
    
    dragMove(e) {
      if(this.draggedItem) {
        this.moveAt(e)
      } else {
        this.isAfterDrop = false
      }
    },
    
    dragged(payload) {
      const {event, player} = payload
      
      this.draggedItem = event.currentTarget.outerHTML
      
      this.draggedStyle.position = 'absolute'
      this.draggedStyle.zIndex = '1001'
      this.draggedStyle.offsetWidth = event.currentTarget.offsetWidth
      this.draggedStyle.offsetHeight = event.currentTarget.offsetHeight
      
      this.moveAt(event);
      
      this.switchPlayers.first = player
    },
    
    dropped() {
      if (this.draggedItem) {
        this.draggedItem = null
        this.isAfterDrop = true
      }
    },
    
    afterDrop(payload) {
      if(this.isAfterDrop) {
    
        const {event, player} = payload
        this.switchPlayers.second = player
        
        if (this.switchPlayers.first.id !== this.switchPlayers.second.id) {
          this.$emit('switchTwoPlayers', this.switchPlayers)
        }
      }
    }
  },
  
  mounted() {
    this.$root.$on('dragMe', (payload) => {
      this.dragged(payload)
    })
    
    this.$root.$on('afterDrop', (payload) => {
      this.afterDrop(payload)
    })
  }
}
</script>

<style scoped>

.wrapper {
  display: flex;
}

.wrapper .match-view{
  border-color: cornflowerblue;
}

</style>