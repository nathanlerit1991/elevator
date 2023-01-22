<template>
  <div>
    <div class="wrapper">
      <div id="building">
        <div id="first_column" class="floor-wrapper">
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

        <div id="fourth_column" class="floor-wrapper">
          <div v-for="i in floors">
            <div class="floors">
              {{ i }}
            </div>
          </div>
        </div>
      </div>

      <div id="status">
        <div>
          <h2>Going Down</h2>
          <p v-html="elevMove || closing  ? 'Next Floor ' + goDown : 'Pending'" />

          <h2>Going Up</h2>
          <p v-html="elevMove || closing ? 'Next Floor ' + goUp : 'Pending'" />
        </div>
        <div class="message">
          <p v-if="closing">Closing for {{ timer }} seconds</p>
          <p v-if="opening">Opening for {{ timer }} seconds</p>
          <p v-if="loadingUnlaoding">Loading / Unloading for {{ timer }} seconds</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      floors: [10, 9, 8, 7, 6, 5, 4, 3, 2, 1],
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
      let elem = document.querySelectorAll('#first_column .floors')

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
            console.log('close left elevator')
          }, interval * increment)

          //Move Elevetor for 10 seconds
          let moveFn = setTimeout(function() {
            elev1.style.top = el * 66 + 'px'
            _this.elevMove = true
            _this.closing = false
            _this.loadingUnlaoding = false
            elem[el].classList.add('active')
            clearTimeout(moveFn)
            console.log('move left elevator', el)
          }, interval * (increment + 1))

          //Open Doors for 10 seconds
          let openFn = setTimeout(function() {
            elev1.classList.remove('active')
            _this.elevMove = false
            _this.loadingUnlaoding = true
            _this.opening = true
            elem[el].classList.remove('active')
            _this.randomPeopleFn()
            clearTimeout(openFn)
            console.log('open left elevator')
          }, interval * (increment + 2))
  
          increment = increment + 3
        }
      })
    },

    //Created 2 functions to create random floor selection
    elevUpFn() {
      let elev2 = document.querySelector('#elev-2')
      let elem = document.querySelectorAll('#fourth_column .floors')
      let elemReverse = []

      let interval = 10000 //  = 10s
      let increment = 1
      let _this = this
      this.randomSecondElevator.forEach(function(el) {

        if (el !== 0) {
          // Since NodeList can't reverse, I created an empty array
          // Push the NodeList into the empty array
          // Reverse the array
          elem.forEach(element => {
            elemReverse.push(element)
          })
          elemReverse.reverse()

          //Close Doors for 10 seconds
          let closeFn = setTimeout(function() {
            elev2.classList.add('active')
            _this.loadingUnlaoding = false
            _this.opening = false
            _this.closing = true
            _this.goUp = el + 1
            clearTimeout(closeFn)
            console.log('close right elevator', el)
          }, interval * increment)

          //Move Elevetor for 10 seconds
          let moveFn = setTimeout(function() {
            elev2.style.bottom = el * 66 + 'px'
            _this.elevMove = true
            _this.closing = false
            _this.loadingUnlaoding = false
            elemReverse[el].classList.add('active')
            clearTimeout(moveFn)
            console.log('move right elevator', el)
          }, interval * (increment + 1))

          //Open Doors for 10 seconds
          let openFn = setTimeout(function() {
            elev2.classList.remove('active')
            _this.elevMove = false
            _this.loadingUnlaoding = true
            _this.opening = true
            elemReverse[el].classList.remove('active')
            _this.randomPeopleFn()
            clearTimeout(openFn)
            console.log('open right elevator', el)
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
    },
  }
}
</script>