<template>
<div id="app">
    <section class="row">
        <div class="small-6 columns">
            <h1 class="text-center">YOU</h1>
            <div class="healthbar">
                <div 
                  class="healthbar text-center" 
                  style="background-color: green; margin: 0; color: white;"
                  :style="{width: playerHealth + '%'}">
                  {{playerHealth}}
                </div>
            </div>
        </div>
        <div class="small-6 columns">
            <h1 class="text-center">KHUM's MONSTER</h1>
            <div class="healthbar">
                <div 
                  class="healthbar text-center" 
                  style="background-color: green; margin: 0; color: white;"
                  :style="{width: monsterHealth + '%'}">
                  {{monsterHealth}}
                </div>
            </div>
        </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
        <div class="small-12 columns">
            <button id="start-game" @click="startGame">START NEW GAME</button>
        </div>
    </section>
    <section class="row controls" v-else>
        <div class="small-12 columns">
            <button id="attack" @click="attack">ATTACK</button>
            <button id="special-attack" @click="specialAttack">SPECIAL ATTACK</button>
            <button id="heal" @click="heal">HEAL</button>
            <button id="give-up" @click="giveUp">GIVE UP</button>
        </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
        <div class="small-12 columns">
            <ul>
                <li v-for="turn in turns"
                :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}" :key="turn.key">
                    {{turn.text}}
                </li>
            </ul>
        </div>
    </section>
</div>
</template>

<script>
export default {
  data() {              // data is a function
    return {
      playerHealth: 100,
      monsterHealth: 100,
      gameIsRunning: false,
      turns: []
    }  
  },
  methods: {            //methods is object
      startGame() {
          this.gameIsRunning = true;
          this.playerHealth = 100;
          this.monsterHealth = 100;
          this.turns = [];
      },
      attack() {
          var damage = this.calculateDamage(3,10)
          this.monsterHealth -= damage;
          this.turns.unshift({
              isPlayer: true,
              text: 'Player hits Monster for ' + damage
          });
          if (this.checkWin()) {
              return;
          }
          this.monsterAttacks();
      },      
      specialAttack() {
          var damage = this.calculateDamage(10,20);
          this.monsterHealth -= damage;
          this.turns.unshift({
              isPlayer: true,
              text: 'Player hits Monster for ' + damage
          });
          if (this.checkWin()) {
              return;
          }
          this.monsterAttacks();
      },
      heal() {
          if (this.playerHealth <= 90) {
              this.playerHealth += 10;
          }else {
              this.playerHealth = 100;
          }
          this.turns.unshift({
              isPlayer: true,
              text: 'Player heals  for 10' 
          });          
          this.monsterAttacks();
      },
      giveUp() {
          this.gameIsRunning = false;
      },
      monsterAttacks() {
          var damage = this.calculateDamage(5,12)
          this.playerHealth -= damage;
          this.checkWin();
          this.turns.unshift({
              isPlayer: false,
              text: 'Monster hits Player for ' + damage
          });
      },
      calculateDamage(min, max) {
          return Math.max(Math.floor(Math.random() * max) + 1, min);
      },
      checkWin() {
           if (this.monsterHealth <= 0) {
              if (confirm('You won! New Game?')){
                  this.startGame();
              } else {
                  this.gameIsRunning = false;
              }            
              return true;
            } else if (this.playerHealth <= 0) {
              if (confirm('You lost! New Game?')){
                  this.startGame();
              } else {
                  this.gameIsRunning = false;
              }
              return true;
            }
            return false;
        }
    }
}
</script>

<style >
@import '../assets/app.css';
</style>
