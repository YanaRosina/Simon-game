<template>
    <div class="container">
      <div class="game-field">
        <button v-on:click="checkUserValue($event)" id="blue" :class="{ 'game-button': true, 'blue': true, 'active': isActive('blue') }">
          <audio ref="blueAudio" src="../assets/audio/blue.mp3"></audio>
        </button>
        <button v-on:click="checkUserValue($event)" id="red" :class="{ 'game-button': true, 'red': true, 'active': isActive('red') }">
          <audio ref="redAudio" src="../assets/audio/red.mp3"></audio>
        </button>
        <button v-on:click="checkUserValue($event)" id="yellow" :class="{ 'game-button': true, 'yellow': true, 'active': isActive('yellow') }">
          <audio ref="yellowAudio" src="../assets/audio/yellow.mp3"></audio>
        </button>
        <button v-on:click="checkUserValue($event)" id="green" :class="{ 'game-button': true, 'green': true, 'active': isActive('green') }">
          <audio ref="greenAudio" src="../assets/audio/green.mp3"></audio>
        </button>
      </div>
      <div class="actions">
        <div class="round">Раунд {{ roundCounter }}</div>
        <button v-on:click="startGame()" class="start">Старт</button>
        <p v-if="youLose">Вы проиграли!</p>

        <div class="mode">
          <p>Уровень сложности</p>
          <input type="radio" id="level1" name="level" value="1500" v-model="pickedLevel">
          <label for="level1">Легкий</label>
          <input type="radio" id="level2" name="level" value="1000" v-model="pickedLevel">
          <label for="level2">Средний</label>
          <input type="radio" id="level3" name="level" value="400" v-model="pickedLevel">
          <label for="level3">Сложный</label>
        </div>
      </div>
    </div>
</template>

<script>

export default {
  data() {
    return {
      youLose: false,
      pickedLevel: 1500,
      activeColor: null,
      roundCounter: 0,
      colorArrow: ['blue', 'red', 'yellow', 'green'],
      generationArray: [],
      clickCounter: 0,
      roundIsActive: false,
    };
  },
  methods: {

    turnButtonOn(color) {
        this.activeColor = color;
        let audioElement = this.$refs[`${color}Audio`];
        audioElement.play();
    },

    isActive(color) {
      return this.activeColor === color;
    },

    turnButtonOff () {
     this.activeColor = null;  
    },

    //Turning the buttons on and off for a certain time
    blinkButton(color,index=0) {
    let timeout =  index * this.pickedLevel;
    setTimeout(() => {   
          this.turnButtonOn(color); 
        }, timeout);

    setTimeout(()=> {
          this.turnButtonOff();    
        }, ((index+1)*this.pickedLevel)-100);  
    },

    colorGeneration () {
      let randIndex = Math.floor (Math.random() * this.colorArrow.length);
      let randColor = this.colorArrow[randIndex];
      this.generationArray.push(randColor);
    },

    //Playback of the generated sequence of buttons
    showSequence () {
      this.roundIsActive = true;
      const changeActiveTime = this.generationArray.length * this.pickedLevel;
      this.generationArray.forEach((item, index) => {
      this.blinkButton(item,index);
       });
      setTimeout(()=>{
        this.roundIsActive = false;
      }, changeActiveTime);
       
    },

    reset(){
      this.generationArray=[];
      this.roundCounter = 0;
      this.clickCounter = 0;
    },

    startGame() {
      if(this.roundCounter > 0) this.reset();
      this.youLose = false;
      this.doRound();
    },

    userLose() {
      this.youLose = true;
      this.reset();
    },

    doRound() {
      this.roundCounter++;
      this.colorGeneration();
      this.showSequence();
      
    },

    checkUserValue(event) {
     let currentColor = event.target.id;
     if((this.roundCounter>0) && !this.roundIsActive) {  //We check if the user can make a click: clicking is not possible if round = 0
      // or the generated sequence is still being played
      this.blinkButton(currentColor);

      if(currentColor == this.generationArray[this.clickCounter]) { //Comparing the user's choice with an element of the generated array
        this.clickCounter++;
        if(this.clickCounter == this.generationArray.length) { //If this is the last element of the current sequence, we move on to the next round
          this.clickCounter = 0;
          setTimeout(() => {
            this.doRound();
          }, 1600);        
        };
        } else {
          this.userLose();
        }
      }
    }

  }

}
</script>

<style lang="sass">

$red: #FF5643
$blue: dodgerblue
$yellow: #FEEF33
$green: #BEDE15
$green-button:#57a957


.container
  display: flex
  align-items: center

.game-field
  width: 400px
  display: flex
  justify-content: center
  flex-wrap: wrap
  height: 400px

  .game-button
    height: 50%
    width: 50%
    opacity: 0.6
    &:hover
      cursor: pointer
      border: 3px solid #fff

  .red
    background-color: $red
    border-radius: 0 100% 0 0

  .blue
    background-color: $blue
    border-radius: 100% 0 0 0

  .yellow
    background-color: $yellow
    border-radius: 0 0 0 100%

  .green
    background-color: $green
    border-radius: 0 0 100% 0

  .active
    opacity: 2

.actions
  margin-left: 100px
  height: 100%
  .round
    padding-bottom: 30px
    font-size: 28px
    font-weight: bold
  .start
    padding: 8px 10px
    text-transform: uppercase
    font-weight: bolder
    font-size: 14px
    letter-spacing: 1px
    width: 120px
  .mode 
    margin-top: 30px
    p 
      font-size: 16px
      font-weight: bold
    label
      margin-right: 10px

  </style>