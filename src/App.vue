<template>
    <div v-show="show == false" id="div_menu">
        <h1 id="">Guerra 🗡️ pokemon</h1>
        <p>"¡Bienvenido a la Guerra Pokémon! ¡Lucha por la gloria!"</p>
        <div class="cards">
            <div class="card red">
                <p class="second-text"  >Elegir Rondas</p>
                <input type="Number" v-model="rounds" class="tip">
            </div>
            <div class="card blue">
                <p>Estadistica a combatir</p>
                <div class="tip">
                    <q-select id="options" v-model="selectedOption" :options="options" label="Elegir estadisticas"
                        filled />
                </div>
            </div>
            <button @click="getData()" class="card green">jugar</button>
        </div>
    </div>


    <!--2da parte-->

    <div v-show="show == true" class="mainContainer">
        <h1>Poke war</h1>
        <p>rondas por jugar {{ rounds }}</p>
        <p>ronda actual = {{ currentRound }}</p>
        <div class="divTitleChallenger">
            <p>{{ challengerOne.name }}</p>
            <p>{{ challengerTwo.name }}</p>
        </div>
        <div class="divContainerImages">
            <img :src="challengerOne.image" id="challengerOne" alt=""><!--concatenar imagenes mañana-->
            <q-icon name="handshake" size="10em" color="red"></q-icon>
            <img :src="challengerTwo.image" alt="Challenger 2" /><!--concatenar imagenes mañana-->
        </div>
        <p v-show="winner != ''">el ganador es {{ winner }} </p>
        <div class="containerButtons">
            <q-btn-group push>
                <q-btn @click="getData()" push label="Siguiente ronda" icon="swap_horiz" />
                <q-btn @click="getWinner()"  :disable="btnDisable" push label="Combatir" icon="play_arrow" />
                <q-btn push label="Inicio" icon="home" @click="show = false" />
            </q-btn-group>
        </div>

    </div>

</template>
<script setup>
import { ref } from 'vue';
import axios from 'axios';

let show = ref(false)
let pokemonId = ref('');
let selectedOption = ref(null); // opcion seleccionada 
let rounds = ref(null);
let currentRound=ref(0)
let winner = ref("")
let btnDisable = ref(false)

let challengerOne= ref({
    image:"",
    name:"",
    Hp:"",
    Attack:"",
    Defense:"",
    Speed:""
})

let challengerTwo= ref({
    image:"",
    name:"",
    Hp:"",
    Attack:"",
    Defense:"",
    Speed:""
})


let options = ref([
    { label: 'Hp', value: 'Hp' },
    { label: 'Attack', value: 'Attack' },
    { label: 'Defense', value: 'Defense' },
    { label: 'Speed', value: 'Speed' }
])

async function getData() {
    let id1 = Math.floor(Math.random() * 898) + 1;
    let id2 = Math.floor(Math.random() * 898) + 1;

    try {
        let [pokemon1, pokemon2] = await Promise.all([
            axios.get(`https://pokeapi.co/api/v2/pokemon/${id1}`),
            axios.get(`https://pokeapi.co/api/v2/pokemon/${id2}`),
        ]);

    
        console.log(pokemon1.data);
        challengerOne.value.image=pokemon1.data.sprites.other["official-artwork"].front_default
        challengerOne.value.name = pokemon1.data.name
        challengerOne.value.Hp = pokemon1.data.stats[0].base_stat;
        challengerOne.value.Attack = pokemon1.data.stats[1].base_stat;
        challengerOne.value.Defense = pokemon1.data.stats[2].base_stat;
        challengerOne.value.Speed = pokemon1.data.stats[5].base_stat;
        console.log(challengerOne.value.Hp);



        challengerTwo.value.image=pokemon2.data.sprites.other["official-artwork"].front_default
        challengerTwo.value.name = pokemon2.data.name
        challengerTwo.value.Hp = pokemon2.data.stats[0].base_stat;
        challengerTwo.value.Attack = pokemon2.data.stats[1].base_stat;
        challengerTwo.value.Defense = pokemon2.data.stats[2].base_stat;
        challengerTwo.value.Speed = pokemon2.data.stats[5].base_stat;
        console.log(challengerTwo.value.Hp);

        show.value = true;
        winner.value = "";
        calculatecurrentRound()


    } catch (error) {
        alert('Pokemon not found');
        show.value = false;
    }
}

    function getWinner(){

        let stat=selectedOption.value.value

        switch (true) {
            case challengerOne.value[stat] > challengerTwo.value[stat]:
            winner.value = challengerOne.value.name
                break;
            
            case challengerOne.value[stat] < challengerTwo.value[stat]:
            winner.value = challengerTwo.value.name
                break;
        
            default:
                winner.value = "empate"
                break;
        }
    }


function calculatecurrentRound (){
    if (currentRound.value < rounds.value){
        currentRound.value++
    }
    else{
        btnDisable.value=true
    }
}
</script>
