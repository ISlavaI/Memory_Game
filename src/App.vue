<template>
  <h1>Card Matching</h1>
  <section class = "game-board">
    <Card
      v-for="(card, index) in cardList"
      :key="`card-${index}`"
      :matched="card.matched"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      @select-card="flipCard"
    />
  </section>
  <h2 v-if="newPlayer === false">{{ status }}</h2>
  <div>
    <button v-if="newPlayer === true" @click="startGame" class="button"> Start Game </button>
  </div>
  <div>
    <button v-if="newPlayer === false" @click="restartGame" class="button"> Restart Game </button>
    </div>
</template>


<script>
import _ from 'lodash'
import { ref, watch, computed } from 'vue'
import Card from './components/Card.vue'

export default {
  name: 'App',
  components:{
    Card
  },
  setup(){
    const cardList = ref([]);
    const userSelection = ref([]);
    const newPlayer = ref(true);
    
    const startGame = () => {
      newPlayer.value = false

      restartGame()

    }

    const status = computed(()=>{
      
      if (remainingPairs.value === 0){
        return 'Congratulations!'
      } else {
        return `Remaining Pairs:${remainingPairs.value}`
      }
    })

    const remainingPairs = computed(()=>{
      const remainingCards = cardList.value.filter(card => card.matched === false).length

      return remainingCards / 2
    })

    const shuffleCards = () => {
      cardList.value =  _.shuffle(cardList.value)
    }

    const restartGame = () => {
      shuffleCards()

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          position: index,
          matched:  false,
          visible:  false
        }
      })
    };

    const cardItems =['javascript', 'laravel', 'nodejs', 'php', 'react', 'svelte', 'typescript', 'vue']
    cardItems.forEach(item =>{

      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false,
      })

      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false,
      })
    })

    cardList.value =cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      }
    })

    const flipCard = payload => {
      cardList.value[payload.position].visible = true

      if(userSelection.value[0]){

        if(userSelection.value[0].position === payload.position 
        && userSelection.value[0].faceValue === payload.faceValue) {
          return
        } else {
          userSelection.value[1] = payload
        }
        userSelection.value[1] = payload
      } else{
        userSelection.value[0] = payload
      }
    }

    watch(
      userSelection, 
      (currentValue) => {

        if(currentValue.length === 2) {
          const cardOne = currentValue[0]
          const cardTwo = currentValue[1]

          if(cardOne.faceValue === cardTwo.faceValue){
            cardList.value[cardOne.position].matched = true
            cardList.value[cardTwo.position].matched = true
          } else {
              setTimeout(() => {
              cardList.value[cardOne.position].visible = false
              cardList.value[cardTwo.position].visible = false
              }, 400)
          } 
          userSelection.value.length = 0
        }
      }, 
      {deep: true}
    )
    return {
      cardList,
      flipCard,
      userSelection,
      status,
      shuffleCards,
      restartGame,
      startGame,
      newPlayer,
    }
  }
}
</script>

<style>
#app {
  font-family: Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #000000;
  padding-top: 60px;
  background-color: #dad8d8;
  height: 100vh;
}

.game-board{
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-column-gap: 20px;
  grid-row-gap: 20px;
  justify-content: center;
}
.button{
background-color: #dbd9d9;
color: #000000;
padding: 10px;
border: 2px solid #000000;
border-radius: 5px;
cursor: pointer;
}
</style>