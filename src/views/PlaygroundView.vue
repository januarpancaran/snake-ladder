<template>
  <div>
    <div id="board">
      <div
        v-for="(player, index) in playerProperties"
        :key="index"
        :class="['playerProperties', 'position-' + player.position]"
      >
      <div class="player-icon">
        <img :src="player.imageUrl" alt="Player Icon" />
      </div>
      </div>
    </div>
    
    <div class="player-wrapper">
      <div class="player-list">
        <div
          v-for="(player, index) in playerProperties"
          :key="index"
          :class="['player', color[index], { active: index === turn }]"
          :style="{ 
            fontSize: index === turn ? '15px' : '15px', 
            padding: index === turn ? '10px 25px' : '10px 25px', 
            borderRadius: index === turn ? '25px' : '25px',
          }"
        >
          <p>Player-{{ index + 1 }}: {{ player.position }}</p>
        </div>
      </div>

      <div class="controls">
        <button
          type="button"
          class="button add-player bg-yellow-500"
          @click="addPlayer"
          :disabled="playerProperties.length >= 3"
        >
          Add Player
        </button>
        <button
          type="button"
          class="button start-game bg-green-500"
          @click="startGame"
          :disabled="gameStarted"
        >
          Start Game
        </button>
        <button
          type="button"
          class="button reset-game bg-red-500"
          @click="resetGame"
          :disabled="!gameStarted"
        >
          Reset Game
        </button>
        <button
          type="button"
          class="button roll-dice bg-gray-500"
          style="color: white"
          @click="rollDice"
          :disabled="diceDisabled || diceRolling"
        >
          Roll Dice
        </button>
        <button
          type="button"
          class="button history-button bg-blue-500"
          @click="showHistory"
        >
          Move(s) History
        </button>
      </div>
    </div>

    <div class="dice-wrapper"> 
      <p id="dice-label">Dice Result  </p>
      <div id="dice-result">{{diceResult}}</div>
    </div>

    <div
      class="history-wrapper" v-if="historyVisible"
    >
      <h3>Move(s) History</h3>
      <div  v-for="(history, index) in movesHistory" :key="index">
        <p><strong>Player-{{ index + 1 }}:</strong></p>
        <div v-for="(turn, turnIndex) in history" :key="turnIndex">
          <p>Turn {{ turnIndex + 1 }}: {{ turn.join(' -> ') }}</p>
        </div>
        <p>
          <strong>Walk:</strong> {{ cleanMovesHistory[index].join(' -> ') }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import pawnImage1 from "@/assets/pawn1.png"
import pawnImage2 from "@/assets/pawn2.png"
import pawnImage3 from "@/assets/pawn3.png"
import pawnImage4 from "@/assets/pawn4.png"
import pawnImage5 from "@/assets/pawn5.png"
export default {
  data() {
    return {
      playerProperties: [{ position: 1, isFinish: false,imageUrl: pawnImage1 }],
      movesHistory: [[]],
      historyVisible: false,
      turn: 0,
      diceRolling: false,
      diceValue: 0,
      diceDisabled: true,
      gameStarted: false,
      color: [
        'bg-red-500' ,
        'bg-blue-500',
        'bg-purple-500',
        'bg-indigo-500',
        'bg-pink-500',
        'bg-teal-500',
        'bg-green-500',
        'bg-yellow-500',
        'bg-orange-500',
        'bg-gray-500'
      ],
      snakes: {
        16: 6,
        46: 25,
        49: 11,
        62: 19,
        64: 60,
        74: 53,
        89: 68,
        92: 88,
        95: 75,
        99: 80
      },
      ladders: {
        2: 38,
        7: 14,
        8: 31,
        15: 26,
        21: 42,
        28: 84,
        36: 44,
        51: 67,
        71: 91,
        78: 98,
        87: 94
      },
      gameIsFinish: false,
      diceAudio: new Audio('/assets/sfx/assets_sfx_roll_dice.mp3'),
      pawnImages: [pawnImage1, pawnImage2, pawnImage3, pawnImage4, pawnImage5]
    }
  },
  computed: {
    diceResult() {
      return this.diceValue
    },
    cleanMovesHistory() {
      return this.movesHistory.map((playerMoves) => {
        const flattenedMoves = playerMoves.flat()

        return flattenedMoves.filter((pos, idx, arr) => idx === 0 || pos !== arr[idx - 1])
      })
    }
  },
  methods: {
    rollDice() {
      this.diceRolling = true
      this.diceAudio.play()
      this.diceAudio.onended = () => {
        this.diceValue = Math.ceil(Math.random() * 6)
        this.diceRolling = false
        this.movePlayer()
      }
    },
    movePlayer() {
      const diceVal = this.diceValue
      const currentPlayer = this.playerProperties[this.turn]
      let targetPosition = currentPlayer.position + diceVal
      let currentStep = currentPlayer.position
      const playerHistory = []

      playerHistory.push(currentStep)
      const moveStep = () => {
        if (currentStep < targetPosition) {
          currentStep++
          currentPlayer.position = currentStep
          playerHistory.push(currentStep)

          setTimeout(moveStep, 300)
        } else {
          const snakePosition = this.snakes[currentStep]
          const ladderPosition = this.ladders[currentStep]

          if (snakePosition) {
            playerHistory.push(snakePosition)
            currentPlayer.position = snakePosition
          } 
          
          if (ladderPosition) {
            playerHistory.push(ladderPosition)
            currentPlayer.position = ladderPosition
          }

          finalizeMove(playerHistory)
        }
      }

      const finalizeMove = (history) => {
        this.movesHistory[this.turn].push(history)

        if (currentPlayer.position >= 100) {
          currentPlayer.position = 100
          currentPlayer.isFinish = true
          setTimeout(() => {
            alert(`Player-${this.turn + 1} wins!`)
          }, 300)
          this.diceDisabled = true
          this.gameIsFinish = true
        } else {
          this.updateTurn(diceVal !== 6)
        }
        this.diceDisabled = false
      }

      this.diceDisabled = true
      moveStep()
    },
    addPlayer() {
      const playerIndex = this.playerProperties.length
      const pawnImage = this.pawnImages[playerIndex % this.pawnImages.length]
      this.playerProperties.push({ position: 1, isFinish: false, imageUrl: pawnImage })
      this.movesHistory.push([])
    },
    startGame() {
      this.diceDisabled = false
      this.gameStarted = true
    },
    resetGame() {
      this.playerProperties = [
        { position: 1, isFinish: false, imageUrl: this.pawnImages[0] }
      ]
      this.movesHistory = [[]]
      this.historyVisible = false
      this.turn = 0
      this.diceValue = 0
      this.gameStarted = false
      this.diceDisabled = true
    },
    updateTurn(skipTurn = true) {
      if (skipTurn) {
        this.turn = (this.turn + 1) % this.playerProperties.length
      }
      this.diceDisabled = false
    },
    showHistory() {
      this.historyVisible = !this.historyVisible
    }
  }
}
</script>

<style scoped>
  #board {
    width: 100%;
    max-width: 600px;
    height: 600px;
    background-image: url('@/assets/board.jpeg');
    background-size: cover;
    background-position: center;
    position: relative;
    margin: 0 auto;
  }
  .playerProperties {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    position: absolute;
    transition: all 0.3s ease;
  }
  .player-icon img {
    width: 85px;
    height: 85px;
    object-fit: cover;
    position: absolute;
    transition: all 0.3s ease;
  }
  .player-list {
    position: absolute; 
    top: 12.5%; 
    left: 20px; 
    display: flex;
    flex-direction: column;
    gap: 10px; 
  }
  .controls {
    position: absolute;
    top: 40%; 
    left: 20px; 
    display: flex;
    flex-direction: column; 
    gap: 10px; 
    z-index: 1000;
  }
  .controls button {
  border-radius: 25px; 
  padding: 7px 14px; 
  display: inline-block;
  }
  .dice-wrapper {
    position: absolute;
    top: 80%; 
    left: 23px; 
    display: flex;
    flex-direction: column; 
    gap: 6px; 
    z-index: 1000;
  }
  #dice-label {
  font-size: 18px;
  font-weight: bold;
  color: #555;
  margin-bottom: 5px;
  text-align: center;
  }
  #dice-result {
  text-align: center;
  }
  .history-wrapper {
    background: rgba(255, 255, 255, 0.9);
    color: black;
    border: 1px solid #000;
  }

  .position--1 { bottom: 7%; left: -7.5%; }
  .position-0 { bottom: 7%; left: -7.5%; }
  .position-1 { bottom: 7%; left: 2.5%; }
  .position-2 { bottom: 7%; left: 12.5%; }
  .position-3 { bottom: 7%; left: 22.5%; }
  .position-4 { bottom: 7%; left: 32.5%; }
  .position-5 { bottom: 7%; left: 42.5%; }
  .position-6 { bottom: 7%; left: 52.5%; }
  .position-7 { bottom: 7%; left: 62.5%; }
  .position-8 { bottom: 7%; left: 72.5%; }
  .position-9 { bottom: 7%; left: 82.5%; }
  .position-10 { bottom: 7%; left: 92.5%; }
  .position-20 { bottom: 17%; left: 2.5%; }
  .position-19 { bottom: 17%; left: 12.5%; }
  .position-18 { bottom: 17%; left: 22.5%; }
  .position-17 { bottom: 17%; left: 32.5%; }
  .position-16 { bottom: 17%; left: 42.5%; }
  .position-15 { bottom: 17%; left: 52.5%; }
  .position-14 { bottom: 17%; left: 62.5%; }
  .position-13 { bottom: 17%; left: 72.5%; }
  .position-12 { bottom: 17%; left: 82.5%; }
  .position-11 { bottom: 17%; left: 92.5%; }
  .position-21 { bottom: 27%; left: 2.5%; }
  .position-22 { bottom: 27%; left: 12.5%; }
  .position-23 { bottom: 27%; left: 22.5%; }
  .position-24 { bottom: 27%; left: 32.5%; }
  .position-25 { bottom: 27%; left: 42.5%; }
  .position-26 { bottom: 27%; left: 52.5%; }
  .position-27 { bottom: 27%; left: 62.5%; }
  .position-28 { bottom: 27%; left: 72.5%; }
  .position-29 { bottom: 27%; left: 82.5%; }
  .position-30 { bottom: 27%; left: 92.5%; }
  .position-40 { bottom: 37%; left: 2.5%; }
  .position-39 { bottom: 37%; left: 12.5%; }
  .position-38 { bottom: 37%; left: 22.5%; }
  .position-37 { bottom: 37%; left: 32.5%; }
  .position-36 { bottom: 37%; left: 42.5%; }
  .position-35 { bottom: 37%; left: 52.5%; }
  .position-34 { bottom: 37%; left: 62.5%; }
  .position-33 { bottom: 37%; left: 72.5%; }
  .position-32 { bottom: 37%; left: 82.5%; }
  .position-31 { bottom: 37%; left: 92.5%; }
  .position-41 { bottom: 47%; left: 2.5%; }
  .position-42 { bottom: 47%; left: 12.5%; }
  .position-43 { bottom: 47%; left: 22.5%; }
  .position-44 { bottom: 47%; left: 32.5%; }
  .position-45 { bottom: 47%; left: 42.5%; }
  .position-46 { bottom: 47%; left: 52.5%; }
  .position-47 { bottom: 47%; left: 62.5%; }
  .position-48 { bottom: 47%; left: 72.5%; }
  .position-49 { bottom: 47%; left: 82.5%; }
  .position-50 { bottom: 47%; left: 92.5%; }
  .position-60 { bottom: 57%; left: 2.5%; }
  .position-59 { bottom: 57%; left: 12.5%; }
  .position-58 { bottom: 57%; left: 22.5%; }
  .position-57 { bottom: 57%; left: 32.5%; }
  .position-56 { bottom: 57%; left: 42.5%; }
  .position-55 { bottom: 57%; left: 52.5%; }
  .position-54 { bottom: 57%; left: 62.5%; }
  .position-53 { bottom: 57%; left: 72.5%; }
  .position-52 { bottom: 57%; left: 82.5%; }
  .position-51 { bottom: 57%; left: 92.5%; }
  .position-61 { bottom: 67%; left: 2.5%; }
  .position-62 { bottom: 67%; left: 12.5%; }
  .position-63 { bottom: 67%; left: 22.5%; }
  .position-64 { bottom: 67%; left: 32.5%; }
  .position-65 { bottom: 67%; left: 42.5%; }
  .position-66 { bottom: 67%; left: 52.5%; }
  .position-67 { bottom: 67%; left: 62.5%; }
  .position-68 { bottom: 67%; left: 72.5%; }
  .position-69 { bottom: 67%; left: 82.5%; }
  .position-70 { bottom: 67%; left: 92.5%; }
  .position-80 { bottom: 77%; left: 2.5%; }
  .position-79 { bottom: 77%; left: 12.5%; }
  .position-78 { bottom: 77%; left: 22.5%; }
  .position-77 { bottom: 77%; left: 32.5%; }
  .position-76 { bottom: 77%; left: 42.5%; }
  .position-75 { bottom: 77%; left: 52.5%; }
  .position-74 { bottom: 77%; left: 62.5%; }
  .position-73 { bottom: 77%; left: 72.5%; }
  .position-72 { bottom: 77%; left: 82.5%; }
  .position-71 { bottom: 77%; left: 92.5%; }
  .position-81 { bottom: 87%; left: 2.5%; }
  .position-82 { bottom: 87%; left: 12.5%; }
  .position-83 { bottom: 87%; left: 22.5%; }
  .position-84 { bottom: 87%; left: 32.5%; }
  .position-85 { bottom: 87%; left: 42.5%; }
  .position-86 { bottom: 87%; left: 52.5%; }
  .position-87 { bottom: 87%; left: 62.5%; }
  .position-88 { bottom: 87%; left: 72.5%; }
  .position-89 { bottom: 87%; left: 82.5%; }
  .position-90 { bottom: 87%; left: 92.5%; }
  .position-100 { bottom: 97%; left: 2.5%; }
  .position-99 { bottom: 97%; left: 12.5%; }
  .position-98 { bottom: 97%; left: 22.5%; }
  .position-97 { bottom: 97%; left: 32.5%; }
  .position-96 { bottom: 97%; left: 42.5%; }
  .position-95 { bottom: 97%; left: 52.5%; }
  .position-94 { bottom: 97%; left: 62.5%; }
  .position-93 { bottom: 97%; left: 72.5%; }
  .position-92 { bottom: 97%; left: 82.5%; }
  .position-91 { bottom: 97%; left: 92.5%; }

</style>