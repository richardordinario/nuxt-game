<template>
  <div class=" w-full h-screen bg-gray-800">
    <div class=" grid grid-cols-3 gap-2 h-screen" v-if="round==1">
      <div class=" col-span-2 flex items-center justify-center">
        <div class=" flex items-center justify-center gap-4 flex-wrap">
          <div v-for="item,i in secondRoundPlayers" :key=i class="border border-gray-500 px-10 py-2 rounded-[20px] text-white text-[75px] font-bold">
            {{ item }}
          </div>
        </div>
      </div>
      <div class=" border-l h-screen border-gray-500 p-4">
        <div class=" mx-4 mt-5 mb-10">
          <Button :disabled="loading" @click="draw" label="Draw" class=" w-full"/>
        </div>
        <div class=" m-4 flex gap-4">
          <div class="">
            <FloatLabel>
              <InputText v-model="player" type="text" class=" bg-transparent border-gray-500 text-gray-300" />
              <label for="username">Player Name</label>
            </FloatLabel>
          </div>
          <div class="">
            <Button @click="addPlayer" label="Add Player" />
          </div>
        </div>
        <div class=" m-4  gap-4">
          <div class=" text-gray-500 font-bold mt-10 mb-5 text-lg">Players</div>
          <div class="flex gap-3 flex-wrap" >
            <div class=" " v-for="item, i in players" :key="i">
              <Chip :class="`${secondRoundPlayers.includes(item) ? ' border-green-500 text-green-500' : '   border-gray-500 text-gray-500 '} border bg-transparent`" :label="item"/>
            </div>
          </div>
        </div>
        <div class=" mx-4 mt-5 mb-10">
          <Button v-if="secondRoundPlayers" @click="round=2" label="Next round" class=" w-full"/>
        </div>
      </div>
    </div>
    <div class=" w-full h-screen flex items-center justify-center" v-else>
      <div class="">
        <div class="absolute right-[27px] top-[105px]">
          <i class="pi pi-flag text-[50px] text-white -ml-[100px]"></i>
        </div>
        <div class="track relative w-[1200px] border-r-4">
          <div class="car my-[0px]" id="car-1">
            <i :class="`pi pi-truck text-[50px] text-white`"></i>
          </div>
          <div class="car my-[75px]" id="car-2">
            <i :class="`pi pi-shopping-cart text-[50px] text-white`"></i>
          </div>
          <div class="car my-[150px]" id="car-3">
            <i :class="`pi pi-truck text-[50px] text-white`"></i>
          </div>
          <div class="car my-[227px]" id="car-4">
            <i :class="`pi pi-shopping-cart text-[50px] text-white`"></i>
          </div>
          <div class="car my-[300px]" id="car-5">
            <i :class="`pi pi-truck text-[50px] text-white`"></i>
          </div>
        </div>
        <div class="flex justify-center mt-10">
          <div class="mx-5">
            <Button  @click="startRace" label="Start Race"/> 
          </div>
          <div class=" flex items-center gap-4">
            <div class="" v-for="item, i in secondRoundPlayers" :key="i">
              <!-- <Chip class=" bg-transparent border border-gray-500 text-gray-500" :label="item"/> -->
              <Button disabled type="button" :label="item" :badge="scores[i]" :class=" ` bg-${colors[i]}-500`"/>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang=ts>
  import FloatLabel from 'primevue/floatlabel';
  import InputText from 'primevue/inputtext';
  import Button from 'primevue/button';
  import Chip from 'primevue/chip';

  const players = ref<any>([
    'jam',
    'abi',
    'nikko',
    'jason',
    'jay',
    'trish',
    'prince',
    'pau',
    // 'serge',
    'renz',
    'brissa',
    'jhobelle',
    'lenard',
    'issa',
    'kyle',
    'lei',
    'laine',
  ])

  const player = ref<string>("")
  const loading = ref<any>(false)
  const secondRoundPlayers = ref<any>([
    'lenard',
    'issa',
    'kyle',
    'lei',
    'laine',
  ])
  const round = ref<any>(1)
  const positions = ref<any>([0, 0, 0, 0, 0]);
  const scores = ref<any>(["0", "0", "0", "0", "0"]);
  const colors = ref<any>(["red", "green", "blue", "orange", "purple"]);
  const carWidth = ref<any>(50);
  const trackWidth = ref<any>(1200)

  const addPlayer = () => {
    unref(players).push(unref(player))
    player.value = ""
  }

  const draw = () => {
    setDeceleratingTimeout(
      () => secondRoundPlayers.value = pickRandomNames(unref(players), 5), 
      10,
      50
    )
  }

  function pickRandomNames(arr: any, num: number) {
    const result = [];
    const shuffled = arr.slice(); // Create a shallow copy of the array to avoid modifying the original array
    let currentIndex = shuffled.length;
    let temporaryValue, randomIndex;

    // While there remain elements to shuffle...
    while (0 !== currentIndex) {
      // Pick a remaining element...
      randomIndex = Math.floor(Math.random() * currentIndex);
      currentIndex -= 1;

      // And swap it with the current element.
      temporaryValue = shuffled[currentIndex];
      shuffled[currentIndex] = shuffled[randomIndex];
      shuffled[randomIndex] = temporaryValue;
    }

    // Get the first 'num' elements from the shuffled array
    for (let i = 0; i < num; i++) {
      result.push(shuffled[i]);
    }

    console.log(result)
    return result;
  }

  function setDeceleratingTimeout(callback: any, factor: any, times: any) {
    var internalCallback = function(t: any, counter: any) {
      return function() {
        if (--t > 0) {
          window.setTimeout( internalCallback, ++counter * factor );
          callback();
        }
      }
    }(times, 0);
    window.setTimeout(internalCallback, factor);
  }

  function moveCars() {
    positions.value = unref(positions).map((pos: any, index:any) => {
        const speed : any= Math.floor(Math.random() * 5) + 1;
        const newPos : any = pos + speed;
        const car : any = document.getElementById(`car-${index}`)
        // console.log(car)
        if(car) {
          car.style.left = newPos + 'px';
          return newPos;
        }
    
    });

    const winnerIndex = unref(positions).findIndex((pos: any) => pos >= unref(trackWidth) - unref(carWidth));
    if (winnerIndex !== -1) {
        alert(`${unref(secondRoundPlayers)[winnerIndex-1]} wins the race!`);
        var score = unref(scores)[winnerIndex-1]
        unref(scores)[winnerIndex-1] = (parseInt(score)+1).toString()
        return;
    }

    requestAnimationFrame(moveCars);
}

function startRace() {
    positions.value = Array(6).fill(0);
    moveCars();
}

</script>

<style scoped>
  .track {
      width: 1200px;
      height: 400px;
      /* border: 1px solid #000; */
      position: relative;
      overflow: hidden;
  }

.car {
    width: 50px;
    height: 50px;
    /* background: red; */
    position: absolute !important;
    /* bottom: 0; */
}
</style>
