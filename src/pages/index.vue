<template>
  <div id="container">
    <h1 id="logo">novak</h1>

    <div id="pointer">
      <div class="player_score">
        <div class="player_name" id="player_1_name">Player 1</div>
        <ul>
          <li>{{ puntos.jugador1[1] }}</li>
          <li>{{ puntos.jugador1[2] }}</li>
          <li>{{ puntos.jugador1[3] }}</li>
        </ul>
      </div>
      <div class="player_score">
        <div class="player_name" id="player_2_name">Player 2</div>
        <ul>
          <li>{{ puntos.jugador2[1] }}</li>
          <li>{{ puntos.jugador2[2] }}</li>
          <li>{{ puntos.jugador2[3] }}</li>
        </ul>
      </div>
    </div>

    <div id="wrapper" v-if="match_alive">
      <div id="court" :style="{ backgroundColor: courtBackgroundColor }">
        <div id="energy">{{ energy }}</div>
        <div id="barr" :class="{ 'ball_home': player_active === 1, 'ball_away': player_active === 2 }">
          <div id="ball"></div>
        </div>
      </div>

      <div id="joystick">
        <div id="controlers">
          <button class="racket" id="racket_p1" @click="shot(1)">P1</button>
          <div id="dice">
            <div>{{ dice }}</div>
          </div>
          <button class="racket" id="racket_p2">COM</button>
        </div>
      </div>
    </div>

    <div id="buttoner">
      <div id="restart_btn" @click="restartMatch">Restart Match</div>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      match_alive: true,
      energy: 0,
      player_active: 1,
      fading_msg: null,
      fadding_2: null,
      showElement: true,
      diceRolling: false,
      dice: 1,
      courtBackgroundColor: '#ffffff33', // Color de fondo inicial del #court
      puntos: {
        jugador1: { 1: 4, 2: 4, 3: 4 },
        jugador2: { 1: 4, 2: 4, 3: 4 },
      },
      current_set: 1,
    };
  },
  methods: {
    rollDice() { // Rola el dado
      this.dice = Math.floor(Math.random() * 6) + 1;
      // console.log("dice", this.dice);

      return this.dice;
    },
    ballBoy(){ // Devuelve la pelota al que tiene el servicio en ese set
      if(this.current_set == 2){
        this.player_active = 2;
      } else {
        this.player_active = 1;
      }
    },
    getSet(){ // Da por ganador el set y cambia al siguiente
      if(this.current_set < 3){
        this.current_set++;

        if(this.current_set == 2){
          setTimeout(() => {
            this.shot(2);
          }, 4000);
        }
      } else {
        console.log("Partido terminado");
        this.match_alive = false;
      }
    },
    getPoint(player){ // Da por ganador el punto y el set si es necesario

      // console.log("punto para", player);

      if(player === 1){
        this.puntos.jugador1[this.current_set]++;

        // console.log(this.puntos.jugador1[this.current_set]);


        if(this.puntos.jugador1[this.current_set] > 5){
          if(this.puntos.jugador1[this.current_set] > 6){
            this.getSet();
          } else {
            if(this.puntos.jugador2[this.current_set] < 5){
              this.getSet();
            }
          }
        }

      } else {
        this.puntos.jugador2[this.current_set]++;

        // console.log(this.puntos.jugador2[this.current_set]);

        if(this.puntos.jugador2[this.current_set] > 5){
          if(this.puntos.jugador2[this.current_set] > 6){
            this.getSet();
          } else {
            if(this.puntos.jugador1[this.current_set] < 5){
              this.getSet();
            }
          }
        }
      }

      this.ballBoy();
    },
    shot(player) { // Hace jugar el punto con show_power y energy
      var shot_power = this.rollDice();

      if(player === 1){
        var anti_player = 2;
        this.player_active = 2;
      } else {
        var anti_player = 1;
        this.player_active = 1;
      }

      // console.log("energy: ",this.energy, "power:", shot_power);

      if(shot_power > this.energy){
        // console.log("hi");
        this.energy = shot_power - this.energy;

        // console.log("new energy: ",this.energy);

        if(this.player_active == 2){
          setTimeout(() => {
            console.log("va a jugar el dos");
            this.shot(2);
          }, 1000);
        }
        
      } else {
        // console.log("hao");
        this.energy = 0;
        this.courtBackgroundColor = '#ff6666'; // Cambiar color de fondo en caso de 'Fail'
        setTimeout(() => {
          this.courtBackgroundColor = '#ffffff33'; // Restaurar color de fondo después de 1 segundo
        }, 1000);

        this.getPoint(anti_player);
      }
    },
    restartMatch() { // Reinicia el puntaje y el estado del juego
      this.puntos = {
        jugador1: { 1: 0, 2: 0, 3: 0 },
        jugador2: { 1: 0, 2: 0, 3: 0 },
      };

      this.energy = 0;
      this.player_active = 1;
      this.courtBackgroundColor = '#ff6666'; // Cambiar color de fondo en caso de 'Fail'
      setTimeout(() => {
        this.courtBackgroundColor = '#ffffff33'; // Restaurar color de fondo después de 1 segundo
      }, 1000);
      this.match_alive = true;
    }
  }
}
</script>

<style scoped>
</style>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

  * {
    font-family: Roboto;
    box-sizing: border-box;
  }

  #container {
    background-color: #f5f5f5; /* Cambié el color de fondo */
    height: 100vh;
    width: 100vw;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: #a16f55;
  }

  h1#logo {
    font-family: 'Montserrat', sans-serif;
    font-size: 64px;
    font-weight: 700;
    color: #333333;
    text-shadow: 2px 2px 4px rgba(255, 255, 255, 0.9);
    margin-bottom: 20px;
    letter-spacing: -6px;
  }

  #joystick {
    width: 100px;
    height: 400px;
    border: 2px solid #dddddd;
    background-color: #ffffff33;
    border-radius: 15px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    display: flex;
    justify-content: center;
    align-items: center;
    display: flex;
    flex-direction: column;
  }

  .points {
    font-size: 24px;
    border: none;
    background-color: #e0e0e0; /* Suavicé el color de fondo */
    color: #333333;
    border-radius: 10px;
    text-align: center;
    display: grid;
    align-items: center;
    padding: 10px;
  }

  #controlers {
    display: flex;
    flex-direction: column;
    gap: 10px;
    justify-content: center;
    align-items: center;
  }

  .racket {
    width: 80px;
    height: 80px;
    font-size: 24px;
    border: none;
    background-color: #4c96af; /* Cambié el color del botón */
    color: #ffffff;
    border-radius: 50%;
    cursor: pointer;
    transition: background-color 0.3s ease;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: -2px 3px 4px 0px rgba(0, 0, 0, 3); 
  }

  #racket_p2{
    box-shadow: unset;
  }

  .racket:hover {
    background-color: #72b5cc; /* Cambié el color al pasar el ratón */
  }

  #buttoner {
    margin-top: 20px;
  }

  #restart_btn {
    padding: 10px 20px;
    font-size: 18px;
    background-color: #f44336; /* Cambié el color del botón */
    color: #ffffff;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 3); /* Añadí una sombra suave */
  }

  #restart_btn:hover {
    background-color: #f1655b; /* Cambié el color al pasar el ratón */
  }

  #wrapper{
    display: flex;
    flex-direction: row-reverse;
    padding: 20px 0px;
    gap: 30px;
  }

  #court {
    border: 2px solid #cccccc; /* Cambié el color del borde */
    width: 150px;
    height: 400px;
    border-radius: 15px;
    display: grid;
    justify-items: center;
    align-items: center;
    transition: background-color 1s ease;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* Añadí una sombra suave */
    background-color: #ffffff33;
  }

  #energy{
    font-size: 28px;
    font-weight: 800;
    color: #5e5e5e;
  }

  #barr {
    width: 30px;
    height: 250px;
    background-color: #849db2;
    display: flex;
    align-items: center;
    border-radius: 15px;
    overflow: hidden;
    border: 1px solid white;
  }

  #barr #ball {
    height: 30px;
    width: 100%;
    background-color: #5e5e5e;
  }

  .ball_home {
    display: flex;
    flex-direction: column;
  }

  .ball_away {
    display: flex;
    flex-direction: column-reverse;
  }

  #dice {
    font-size: 48px;
    font-weight: bold;
    color: #ffffff;
    background-color: #9e9e9e; /* Cambié el color de fondo */
    width: 80px;
    height: 80px;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1); /* Suavicé la sombra */
    margin: 10px 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* Añadí una sombra suave */
  }

  .dice-roll {
    transform: scale(1.5);
  }

  #pointer {
    display: flex;
    flex-direction: column;
    background-color: #5e5e5e;
    width: 300px;
    height: 100px;
    text-align: left;
    justify-content: center;
    color: #e0e0e0;
    border: 2px solid #cccccc;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    border-radius: 15px;
  }

  .player_score {
    display: flex;
    align-items: center;
    font-size: 24px;
    height: 50px;
  }

  .player_score ul{
    display: flex;
    flex-direction: row;
    margin: 0px;
    gap: 10px;
    margin: 0 auto;
  }

  .player_name{
    text-indent: 10px;
  }

  .player_score li {
    list-style: none;
  }


  @media only screen and (max-width: 600px) {
    #container {
      justify-content: start;
    }

  }
</style>