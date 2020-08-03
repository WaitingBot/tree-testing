<template>
  <div class="wrapper">
    
    <div class="controls">
      <InputPlayers
          @newPlayersNumber="generateMatches"
      />
      
      <button class="btn-shuffle" @click="shufflePlayers">Перемешать</button>
      
      <InputWinPlayer
          @newWinner="createWinner"
      />
    </div>
    
    
    <MatchList
        :matches="this.matches"
        @switchTwoPlayers="switchPlayers"
    />
  </div>
</template>

<script>
import InputPlayers from './InputPlayers';
import InputWinPlayer from './InputWinPlayer';
import MatchList from './MatchList';

function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    let j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
}

export default {
  name: "Tournament",
  components: {
    InputPlayers, MatchList, InputWinPlayer
  },
  data() {
    return {
      matches: {},
      rounds: 0,
      count: -1,
      playersGenerated: 0,
      playersList: [],
      replaceNum: 0
    }
  },
  
  methods: {
    genPlayer() {
      const id = this.playersGenerated + 1
      this.playersGenerated += 1
      
      const name = 'Игрок ' + id
      const newPlayer = {
        name,
        id
      }
  
      this.playersList.push(newPlayer)
      return newPlayer
    },
    
    genTree(depth) {
      this.count++
      let name = '';
      
      if(depth < 1) {
        const players = []
        players.push(this.genPlayer())
        players.push(this.genPlayer())
        
        return {
          name: name,
          children: null,
          players
        }
      }
      
      let ar = new Array(2);
      for(let i=0; i < ar.length; i++) {
        ar[i] = this.genTree(depth-1);
      }
      return  { name:name, children:ar, players: [], winId: null };
},

    generateMatches(players) {
      this.playersNumber = players
      this.playersGenerated = 0
      this.rounds = 0
      this.playersList = []
      do {
        players = Math.floor(players/2)
        this.rounds += 1
      } while(players !== 0)
      
      this.count = -1
      
      this.matches = Object.assign({}, this.genTree(this.rounds - 2))
      
    },
    
    replacePlayers(match) {
      match.name = ''
      match.winId = null
      
      if(match.children === null) {
        match.players.map(pl => {
          const newPlayer = this.playersList[this.replaceNum]
          
          pl.name = newPlayer.name
          pl.id = newPlayer.id
          //
          this.replaceNum += 1
          
          return pl
        })
        
        match = match.players.slice()
      } else {
        match.children.forEach(ch => this.replacePlayers(ch))
      }
    },
  
    shufflePlayers() {
      shuffle(this.playersList)
      
      this.replaceNum = 0
      
      let copyMatches = JSON.parse(JSON.stringify(this.matches))
      this.replacePlayers(copyMatches)
      
      this.matches = Object.assign({}, copyMatches)
  
    },
    
    findStartPlayer(match, players) {
      if (match.children === null) {
        match.players.forEach(pl => {
          if (pl.id === players.first.id) {
            pl.id = players.second.id
            pl.name = players.second.name
            return pl
          } else if (pl.id === players.second.id) {
            pl.id = players.first.id
            pl.name = players.first.name
            return pl
          }
        })
      } else {
        match.children.forEach(m => this.findStartPlayer(m, players))
      }
      
    },
    
    switchPlayers(players) {
      const index2 = this.playersList.findIndex(item => item.id === players.first.id);
      const index1 = this.playersList.findIndex(item => item.id === players.second.id);
      
      [this.playersList[index1], this.playersList[index2]] = [this.playersList[index2], this.playersList[index1]]
      
      this.replaceNum = 0
      let copyMatches = JSON.parse(JSON.stringify(this.matches))
      this.replacePlayers(copyMatches)
      this.matches = Object.assign({}, copyMatches)
    },
    
    findWinner(match, winPlayer) {
      if (match.children) {
        match.children.forEach(pl => {
          if (pl.winId === winPlayer.id) {
            match.name = winPlayer.name
            match.winId = winPlayer.id
          } else {
            this.findWinner(pl, winPlayer)
          }
        })
      } else {
        match.players.forEach(pl => {
          if (pl.id === winPlayer.id) {
            match.name = winPlayer.name
            match.winId = winPlayer.id
          }
        })
      }
    },
    
    createWinner(id) {
      const player = this.playersList.find(el => el.id === parseInt(id))
  
      let copyMatches = {...this.matches}
      this.findWinner(copyMatches, player)
      this.matches = copyMatches
      //this.matches = copyMatches
    }
    
  },
  

}
</script>

<style scoped lang="scss">

.wrapper {
  margin-left: 20px;
}

.btn-shuffle{
  padding: 10px 10px;
  font-size: 16px;
  background-color: cornflowerblue;
  color: #fff;
  cursor: pointer;
  border: none;
  &:hover{
      background-color: lighten(cornflowerblue, 7%);
   }
  &:focus {
    outline: none;
  }
}

</style>