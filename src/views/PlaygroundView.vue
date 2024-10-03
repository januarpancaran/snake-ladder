<template>
  <div>
    <div id="board">
      <div
        v-for="(player, index) in playerProfiles"
        :key="index"
        :class="['playerProperties', color[index], 'position-' + player.position]"
      ></div>
    </div>

    <div class="player-wrapper">
      <div class="player-list">
        <div
          v-for="(player, index) in playerProperties"
          :key="index"
          :class="['player', color[index], { active: index === turn }]"
        >
          <p>Player-{{ index + 1 }}: {{ player.position }}</p>
        </div>
      </div>

      <div class="controls flex space-x-4 mb-4">
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
          :disabled="diceDisabled"
        >
          Roll Dice
        </button>
      </div>
    </div>

    <div class="dice-wrapper">
      <p id="dice-result">{{ diceResult }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      playerProperties: [{ position: -1, isFinish: false }],
      turn: 0,
      diceRolling: false,
      diceValue: 0,
      diceDisabled: true,
      gameStarted: false,
      color: [
        'bg-red-500',
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
      diceAudio: new Audio('/assets/sfx/assets_sfx_roll_dice.mp3')
    }
  },
  computed: {
    diceResult() {
      return this.diceValue
    }
  },
  methods: {
    rollDice() {
      this.diceRolling = true
      this.diceValue = Math.ceil(Math.random() * 6)
      this.diceAudio.play()
      this.diceAudio.onended = () => {
        this.diceRolling = false
        this.movePlayer()
      }
    },
    movePlayer() {
      const diceVal = this.diceValue
      let currentPlayer = this.playerProperties[this.turn]

      if (currentPlayer.position === -1) {
        if (diceVal === 6) {
          currentPlayer.position = 0
        } else {
          this.updateTurn()
        }
      } else {
        currentPlayer.position += diceVal

        const snakePosition = this.snakes[currentPlayer.position]
        const ladderPosition = this.ladders[currentPlayer.position]

        if (snakePosition) currentPlayer.position = snakePosition
        if (ladderPosition) currentPlayer.position = ladderPosition

        if (currentPlayer.position >= 100) {
          currentPlayer.position = 100
          currentPlayer.isFinish = true
          alert(`Player-${this.turn + 1} wins!`)
          this.updateTurn()
        } else {
          this.updateTurn(diceVal !== 6)
        }
      }
      this.disabled = false
    },
    addPlayer() {
      this.playerProperties.push({ position: -1, isFinish: false })
    },
    startGame() {
      this.diceDisabled = false
      this.gameStarted = true
    },
    resetGame() {
      this.playerProperties = [{ position: -1, isFinish: false }]
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
    }
  }
}
</script>
