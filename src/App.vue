<template>
  <div>
    <div class="wrapper">
      <div id="building">
        <div class="floor-wrapper">
          <div v-for="i in floors">
            <div class="floors">
              {{ i }}
            </div>
          </div>
        </div>

        <div class="elevator-wrapper">
          <div id="elev-1" class="elevator">
            <img v-for="n in firstRandomPeople" src="@/assets/dance-cartoon.gif"/>
          </div>
        </div>
        <div class="elevator-wrapper">
          <div id="elev-2" class="elevator">
            <img v-for="n in secondRandomPeople" src="@/assets/dance-cartoon.gif"/>
          </div>
        </div>

        <div class="floor-wrapper">
          <div v-for="i in floors">
            <div class="floors">
              {{ i }}
            </div>
          </div>
        </div>
      </div>

      <div id="status">
        <h2 v-if="closing">Closing for {{ timer }} seconds</h2>
        <h2 v-if="opening">Opening for {{ timer }} seconds</h2>
        <h2 v-if="loadingUnlaoding">Loading / Unloading for {{ timer }} seconds</h2>
        <div>
          <h2>Going Down</h2>
          <p v-html="elevMove ? 'Next Floor ' + goDown : 'Pending'" />

          <h2>Going Up</h2>
          <p v-html="elevMove ? 'Next Floor ' + goUp : 'Pending'" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      floors: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].reverse(),
      firstRandomPeople: 0,
      secondRandomPeople: 0,
      elevMove: false,
      loadingUnlaoding: true,
      closing: false,
      opening: false,
      timer: 10,
      interval: null,
      goDown: null,
      goUp: null
    }
  },
  mounted() {
    this.elevUpFn()
    this.elevDownFn()
    this.randomPeopleFn()
    this.triggerTimer()
  },
  computed: {
    //Created 2 computed properties to create 2 random array results
    //The result will loop in methods
    randomFirstElevator () {
      let random = Array.from({length: 10}, () => Math.floor(Math.random() * 10) + 0)
      let noRepeat = []
      random.forEach(element => {
        if(!noRepeat.includes(element)) {
          noRepeat.push(element)
        }
      })
      noRepeat.sort(function(a, b){
        return a - b
      })
      return noRepeat 
    },

    //Created 2 computed properties to create 2 random array results
    //The result will loop in methods
    randomSecondElevator () {
      let random = Array.from({length: 10}, () => Math.floor(Math.random() * 10) + 0)
      let noRepeat = []
      random.forEach(element => {
        if(!noRepeat.includes(element)) {
          noRepeat.push(element)
        }
      })
      noRepeat.sort(function(a, b){
        return a - b
      })
      return noRepeat 
    }
  },
  methods: {
    triggerTimer() {
      this.interval = setInterval(this.decrementTime, 1000)
    },
    decrementTime() {
      this.timer = parseInt(this.timer) - 1
      if(this.timer === 0) {
        this.timer = 10
      }
    },
    //Created 2 functions to create random floor selection
    elevDownFn() {
      let elev1 = document.querySelector('#elev-1')

      let interval = 10000 //  = 10s
      let increment = 1
      let _this = this
 
      this.randomFirstElevator.forEach(function(el) {
        if (el !== 0) {
          //Close Doors for 10 seconds
          let closeFn = setTimeout(function() {
            elev1.classList.add('active')
            _this.loadingUnlaoding = false
            _this.opening = false
            _this.closing = true
            _this.goDown = _this.floors[el]
            clearTimeout(closeFn)
            console.log('close')
          }, interval * increment)

          //Move Elevetor for 10 seconds
          let moveFn = setTimeout(function() {
            elev1.style.top = el * 66 + 'px'
            _this.elevMove = true
            _this.closing = false
            _this.loadingUnlaoding = false
            clearTimeout(moveFn)
            console.log('move', el)
          }, interval * (increment + 1))

          //Open Doors for 10 seconds
          let openFn = setTimeout(function() {
            elev1.classList.remove('active')
            _this.elevMove = false
            _this.loadingUnlaoding = true
            _this.opening = true
            _this.randomPeopleFn()
            clearTimeout(openFn)
            console.log('open')
          }, interval * (increment + 2))
  
          increment = increment + 3
        }
      })
    },

    //Created 2 functions to create random floor selection
    elevUpFn() {
      let elev2 = document.querySelector('#elev-2')

      let interval = 10000 //  = 10s
      let increment = 1
      let _this = this
      this.randomSecondElevator.forEach(function(el) {
        if (el !== 0) {
          //Close Doors for 10 seconds
          let closeFn = setTimeout(function() {
            elev2.classList.add('active')
            _this.loadingUnlaoding = false
            _this.opening = false
            _this.closing = true
            _this.goUp = el + 1
            clearTimeout(closeFn)
            console.log('close')
          }, interval * increment)

          //Move Elevetor for 10 seconds
          let moveFn = setTimeout(function() {
            elev2.style.bottom = el * 66 + 'px'
            _this.elevMove = true
            _this.closing = false
            _this.loadingUnlaoding = false
            clearTimeout(moveFn)
            console.log('move', el)
          }, interval * (increment + 1))

          //Open Doors for 10 seconds
          let openFn = setTimeout(function() {
            elev2.classList.remove('active')
            _this.elevMove = false
            _this.loadingUnlaoding = true
            _this.opening = true
            _this.randomPeopleFn()
            clearTimeout(openFn)
            console.log('open')
          }, interval * (increment + 2))

       
          increment = increment + 3
        }
      })
    },
    //Dancing GIF image
    //Create random numbers of images inside the elevator
    randomPeopleFn() {
       this.firstRandomPeople = Math.floor(Math.random() * 5) + 1
       this.secondRandomPeople = Math.floor(Math.random() * 5) + 1
       console.log('TRUE')
    },
  }
}
</script>