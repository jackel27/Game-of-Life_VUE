<style scoped>
.menu {
  text-align: center;
}
#Field {
  margin: 0 auto;
  z-index: -1;
  background-color: gray;
  width: 500px;
  height: 500px;
  display: flex;
  flex-flow: row wrap;
  user-select: none;
}
.Cell {
  z-index: 999999;
  width:  25px;
  height:   25px;
  color:  white;
}
.dead {
  /* z-index: 999999; */
  background-color: black;
}
.alive {
  background-color:  red;
}
</style>

<template>
  <div>
    <div class="menu">
      <button @click="random()">Random</button>
      <button @click="start()">Start</button>
      <button @click="stopIt()">Stop</button>
      <button @click="clearField()">Clear</button>
    </div>
    <div id="Field">
      <cell @click.native="cellSelect(cell)" :cellid="cell" v-for="(cell, key) in cells" :key="key"
      class="dead" :id="cell"></cell>
    </div>
  </div>
</template>

<script>
import Cell from '@/components/Cell'
export default {
  name: 'Field',
  components: {
    Cell
  },
  data () {
    return {
      stop: false,
      nextLife: [],
      width: 500,
      height: 500,
      cells: 400
    }
  },
  methods: {
    clearField () {
      for (let x = 1; x < this.cells; x++) {
        if (document.getElementById(x).classList[1] === 'alive') {
          document.getElementById(x).classList.remove('alive')
          document.getElementById(x).classList.add('dead')
          document.getElementById(x).style.backgroundColor = 'black'
        }
      }
    },
    stopIt () {
      this.stop = true
    },
    start () {
      this.stop = false
      for (let x = 1; x < this.cells; x++) {
        this.check(x)
      }
      this.replace()
      setTimeout(() => {
        if (this.stop === false) {
          this.start()
        }
      }, 100)
    },
    randomColor () {
      let letters = '0123456789ABCDEF'
      let color = '#'
      for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)]
      }
      return color
    },
    replace () {
      for (let x = 0; x < this.nextLife.length; x++) {
        if (this.nextLife[x] === 'alive') {
          document.getElementById(x + 1).classList.remove('dead')
          document.getElementById(x + 1).classList.add('alive')
          document.getElementById(x + 1).style.backgroundColor = this.randomColor()
        } else {
          document.getElementById(x + 1).style.backgroundColor = 'black'
          document.getElementById(x + 1).classList.remove('alive')
          document.getElementById(x + 1).classList.add('dead')
        }
      }
    },
    random () {
      for (let x = 1; x < this.cells; x++) {
        if (Math.random() >= 0.8) {
          if (document.getElementById(x).classList[1] === 'dead') {
            document.getElementById(x).classList.remove('dead')
            document.getElementById(x).classList.add('alive')
            document.getElementById(x).style.backgroundColor = this.randomColor()
          }
        }
      }
    },
    check (id) {
      let surroundingcells = {}
      // check for corners
      if (id === 1) {
        // top left corner
        surroundingcells = {
          topL: 400,
          topM: 381,
          topR: 382,
          midL: 20,
          midR: 2,
          botL: 40,
          bot: 21,
          botR: 22
        }
      } else if (id === 20) {
        // top right corner
        surroundingcells = {
          topL: 399,
          topM: 400,
          topR: 381,
          midL: 19,
          midR: 1,
          botL: 39,
          bot: 40,
          botR: 21
        }
      } else if (id === 381) {
        // bottom left corner
        surroundingcells = {
          topL: 380,
          topM: 361,
          topR: 362,
          midL: 400,
          midR: 382,
          botL: 20,
          bot: 1,
          botR: 2
        }
      } else if (id === 400) {
        // bottom right corner
        surroundingcells = {
          topL: 379,
          topM: 380,
          topR: 361,
          midL: 399,
          midR: 381,
          botL: 19,
          bot: 20,
          botR: 1
        }
        // now check for top row
      } else if (id > 1 && id < 20) {
        surroundingcells = {
          topL: id + 379,
          topM: id + 380,
          topR: id + 381,
          midL: id - 1,
          midR: id + 1,
          botL: id + 19,
          bot: id + 20,
          botR: id + 21
        }
        // check if bottom row
      } else if (id > 381 && id < 400) {
        surroundingcells = {
          topL: id - 21,
          topM: id - 20,
          topR: id - 19,
          midL: id - 1,
          midR: id + 1,
          botL: id - 381,
          bot: id - 380,
          botR: id - 379
        }
        // check if right column
      } else if (id % 20 === 0) {
        surroundingcells = {
          topL: id - 21,
          topM: id - 20,
          topR: id - 39,
          midL: id - 1,
          midR: id - 19,
          botL: id + 19,
          bot: id + 20,
          botR: id + 1
        }
        // check if left column
      } else if ((id % 20 === 1) || (id === 1)) {
        surroundingcells = {
          topL: id - 1,
          topM: id - 20,
          topR: id - 19,
          midL: id + 19,
          midR: id + 1,
          botL: id + 39,
          bot: id + 20,
          botR: id + 21
        }
        // Finally, if none of the above exist, apply the same algorithm for all other cells..
      } else {
        surroundingcells = {
          topL: id - 21,
          topM: id - 20,
          topR: id - 19,
          midL: id - 1,
          midR: id + 1,
          botL: id + 19,
          bot: id + 20,
          botR: id + 21
        }
      }
      let totalLiveNeighbors = 0
      Object.keys(surroundingcells).forEach((key) => {
        if (document.getElementById(surroundingcells[key]).classList[1] === 'alive') {
          totalLiveNeighbors++
        }
      })
      if (document.getElementById(id).classList[1] === 'alive') {
        if (totalLiveNeighbors !== 2 && totalLiveNeighbors !== 3) {
          this.nextLife[id - 1] = 'dead'
        } else {
          this.nextLife[id - 1] = 'alive'
        }
      } else if (totalLiveNeighbors === 3) {
        this.nextLife[id - 1] = 'alive'
      } else {
        this.nextLife[id - 1] = 'dead'
      }
    },
    cellSelect (id) {
      if (document.getElementById(id).classList[1] === 'dead') {
        document.getElementById(id).classList.remove('dead')
        document.getElementById(id).classList.add('alive')
        document.getElementById(id).style.backgroundColor = this.randomColor()
      } else {
        document.getElementById(id).classList.remove('alive')
        document.getElementById(id).classList.add('dead')
        document.getElementById(id).style.backgroundColor = 'black'
      }
      this.check(id)
    }
  }
}
</script>
