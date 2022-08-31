<script>
const empty = 'https://i.postimg.cc/NjrgpcDJ/empty-hole.png';
const anim = 'https://i.postimg.cc/BQSpTZS3/anim-ham.gif';
const hit = 'https://i.postimg.cc/7h6VyXQ6/hit-ham.png';
const snake = 'https://i.postimg.cc/NMRwR5X3/anim-snake.gif';
const heart = 'https://i.postimg.cc/qqsj2JLJ/heart.png';
const ten = 'https://iili.io/rP4UNI.th.gif';
const heart_hole = 'https://iili.io/rPHyjs.th.gif';

const no_cells = 36;
const rand_bool_stack = new Array(100).fill(false);

function fn_grid(){
 let arr = [];
 for(let i = 0; i < no_cells; i++){
  arr.push({bg:empty,hamster:false,snake:false,heart:false,ten:false})
 }
 return arr;
}

let cells = fn_grid();
let score = 0;
let time = 60;
let life = 3;
let speed = 1300;
let num_snake = 6;
let life_stack = [heart,heart,heart];
let gameOver = false;
let gamePause = true;
let gameState = "Start";

$: score_str = `Score ${score}`;

function handle_click(i){
 if(cells[i].snake){
   life = life - 1;
   life_stack.length = life_stack.length - 1;
   cells[i].snake = false;
 }

 if(cells[i].hamster){
     cells[i].hamster = false;
     score = score +1;
     cells[i].bg = hit;
  }
  if(cells[i].ten){
     time = time + 10;
     cells[i].bg= empty;
     cells[i].ten = false;
  }
  if(cells[i].heart){
    life = life + 1;
    life_stack = [...life_stack,heart];
    cells[i].bg = empty;
    cells[i].heart = false;
  }
}

function up_level(x){
  if(score === x){
    num_snake = num_snake + 3;
    speed = speed - 100;
  }
}

rand_bool_stack[Math.floor(Math.random() * rand_bool_stack.length)] = true;

function update(){
   up_level(5);
   up_level(10);
   up_level(15);
   up_level(20);
   up_level(30);
   up_level(40);
   up_level(50);
   up_level(60);

  cells = fn_grid();

 let snake_rand_stack = [];
 let rand = Math.floor(Math.random()*cells.length);
 let rand_heart = Math.floor(Math.random()*cells.length);
 let rand_ten = Math.floor(Math.random()*cells.length);
  
  for(let i = 0; i < num_snake; i++){
    let snake_rand_num = Math.floor(Math.random() * cells.length)
    if(snake_rand_num !== rand){
      snake_rand_stack.push(snake_rand_num);
    }
  }

 if(!gameOver && !gamePause){
  cells[rand].bg = anim;
  cells[rand].hamster = true;
  for(let i = 0; i<snake_rand_stack.length; i++){
    cells[snake_rand_stack[i]].bg = snake;
    cells[snake_rand_stack[i]].snake = true;
    if(rand_ten !== snake_rand_stack[i] && rand_ten !== rand_heart && rand_ten !== rand && time < 40){
      let temp_rand = Math.floor(Math.random()*rand_bool_stack.length);
        if(rand_bool_stack[temp_rand]){
          cells[rand_ten].bg = ten;
          cells[rand_ten].ten = true;
      }
    }
    if(rand_heart !== snake_rand_stack[i] && rand_heart !== rand_ten && rand_heart !== rand && life_stack.length < 3){
      let temp_rand = Math.floor(Math.random() * rand_bool_stack.length);
       if(rand_bool_stack[temp_rand]){
         cells[rand_heart].bg = heart_hole;
         cells[rand_heart].heart = true;
      }
    }
  }

  }
  if(time <= 0 || life <= 0){
    gameOver = true;
    time = "Game Over!";
  }
}

function count_down(){
  if(time >= 1 && life >= 1 && !gamePause){
  time = time - 1;
  }
}

function handle_state(){
 if(!gameOver){
  gamePause = !gamePause;
  if(gameState === "Start"){
     gameState = "Pause"
  }else{
     gameState = "Start";
  }
 }
}

function reset(){
   cells = fn_grid();
   score = 0;
   time = 60;
   gameOver = false;
   gamePause = false;
   life = 3;
   num_snake = 6;
   speed = 1300;
   life_stack = [heart,heart,heart];
}

setInterval(update,speed)
setInterval(count_down,1000)

</script>
<main>
<div class="status">
<div class="lifes">
{#each life_stack as life}
<div class="life" style="background-image: url('{life}');"></div>
{/each}
</div>
<h1>{score_str}</h1>
</div>
<div class="grid">
{#each cells as cell,i}
<button style="background-image: url('{cell.bg}');" on:click={() => handle_click(i)} >{" "}</button>
{/each}
<h1>{time}</h1>
</div>
<div class="control">
<button class="reset" on:click={reset}>RESET</button>
<button class="state" on:click={handle_state}>{gameState}</button>
</div>
</main>
<style>
main{
  zoom:0.5;
  padding-bottom: 70%;
}
.grid{
  display: grid;
  background-color: #d97643;
  grid-template-columns: repeat(6,128px);
}
.reset{
  border: none;
  background-color: green;
  float: right;
  font-size:30px;
  width: 128px;
  height: 60px;
}
button{
  height:128px;
  font-size: 25px;
  text-align: center;
  border: none;
  border-radius: 0%;
  background-image:url();
  padding: 10px 10px 10px 10px;
}

h1{
  font-weight: bolder;
  font-size: 65px;
}

.life{
 height: 128px;
 width:  128px;
}

.lifes{
 display: flex;
 width: auto;
}

.status{
  display: flex;
  justify-content: space-between;
}
.state{
  font-size: 30px;
  width: 128px;
  height: 60px;
  background-color: blue;
}
.control{
 display: flex;
 justify-content: space-between;
}
</style>
