<template>
    <div v-show="showMenu" id="div_menu">
        <h1 id="">Guerra pokemon</h1>
        <p>"¡Bienvenido a la Guerra Pokémon! ¡Lucha por la gloria!"</p>
        <div class="cards">
            <div class="card red">
                <p class="second-text">Numero de rondas</p>
                <input type="Number" id="rounds" v-model="rounds" class="tip">
            </div>
            <div class="card blue">
                <p>Estadistica a combatir</p>
                <div class="tip">
                    <q-select id="options" v-model="selectedOption" :options="options" label="Elegir estadisticas"
                        filled />
                </div>
            </div>
            <button @click="getData()" class="card green">Ir a la batalla</button>
        </div>
    </div>


    <!--2da parte-->

    <div v-show="showBattle" class="mainContainer">
        <h1>Poke war</h1>
        <div class="information">
            <p>rondas por jugar {{ rounds }}</p>
            <p>ronda actual = {{ currentRound }}</p>
            <p>puntos equipo 1 = {{ challengerOne.points }}</p>
            <p>puntos equipo 2 = {{ challengerTwo.points }}</p>
        </div>

      
        <div class="divContainerImages">
            <div id="challengerOne">
                <h2>{{  challengerOne.name}}</h2>
            <img :src="challengerOne.image" id="challengerOne" alt="">
            </div>

            <img src="./assets/fightIcon.png" id="fightIcon" alt="">

            <div id="challengerTwo">
                <h2>{{  challengerTwo.name}}</h2>
            <img :src="challengerTwo.image" alt="Challenger 2" /><!--concatenar imagenes mañana-->
            </div>
        </div>
        <div class="divContainerButtons">
            <p v-show="showTextWinner">el ganador es {{ winner }} </p>
            <q-btn-group push>
                <q-btn @click="nextRound()" :disable="btnDisable" push label="Siguiente ronda" icon="swap_horiz" />
                <q-btn @click="getWinner()" :disable="btnDisable" push label="Combatir" icon="play_arrow" />
                <q-btn push label="Inicio" icon="home" @click="show = false" />
            </q-btn-group>
        </div>
        

    </div>


    <!--3ra parte-->
    
    <div class="menuEnd" v-show="showMenuEnd">
        <h1>La batalla ha finalizado</h1>
        <p>La victoria fue para el {{ winner }} </p>
        <q-btn push label="Volver al menu" icon="home" @click="showMenu = true ; showMenuEnd = false" />

    </div>

</template>
<script setup>
import { ref } from 'vue';
import axios from 'axios';

let showMenu = ref(true);
let showBattle = ref(false);
let showMenuEnd = ref(false);
let showTextWinner = ref(false);
let selectedOption = ref(null); // opcion seleccionada 
let rounds = ref(null);
let currentRound = ref(0)
let winner = ref("")
let btnDisable = ref(false)

let challengerOne = ref({
    image: "",
    name: "",
    Hp: "",
    Attack: "",
    Defense: "",
    Speed: "",
    points: 0
})

let challengerTwo = ref({
    image: "",
    name: "",
    Hp: "",
    Attack: "",
    Defense: "",
    Speed: "",
    points: 0
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
        challengerOne.value.image = pokemon1.data.sprites.other["official-artwork"].front_default
        challengerOne.value.name = pokemon1.data.name
        challengerOne.value.Hp = pokemon1.data.stats[0].base_stat;
        challengerOne.value.Attack = pokemon1.data.stats[1].base_stat;
        challengerOne.value.Defense = pokemon1.data.stats[2].base_stat;
        challengerOne.value.Speed = pokemon1.data.stats[5].base_stat;
        console.log(challengerOne.value.Hp);



        challengerTwo.value.image = pokemon2.data.sprites.other["official-artwork"].front_default
        challengerTwo.value.name = pokemon2.data.name
        challengerTwo.value.Hp = pokemon2.data.stats[0].base_stat;
        challengerTwo.value.Attack = pokemon2.data.stats[1].base_stat;
        challengerTwo.value.Defense = pokemon2.data.stats[2].base_stat;
        challengerTwo.value.Speed = pokemon2.data.stats[5].base_stat;
        console.log(challengerTwo.value.Hp);

        showMenu.value = false;
        showBattle.value = true;
        calculatecurrentRound()


    } catch (error) {
        alert('Pokemon not found');
        show.value = false;
    }
}

function getWinner() {

    let stat = selectedOption.value.value

    switch (true) {
        case challengerOne.value[stat] > challengerTwo.value[stat]:
            winner.value = challengerOne.value.name
            challengerOne.value.points++
            break;

        case challengerOne.value[stat] < challengerTwo.value[stat]:
            winner.value = challengerTwo.value.name
            challengerTwo.value.points++
            break;

        default:
            winner.value = "empate"
            challengerOne.value.points++
            challengerTwo.value.points++
            break;
    }
    showTextWinner.value = true
}


async function nextRound() {
    let replacement = Math.floor(Math.random() * 898) + 1;
    try {
        if (winner.value === challengerOne.value.name) {
            let replacementPokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${replacement}`);
            challengerTwo.value.image = replacementPokemon.data.sprites.other["official-artwork"].front_default
            challengerTwo.value.name = replacementPokemon.data.name
            challengerTwo.value.Hp = replacementPokemon.data.stats[0].base_stat;
            challengerTwo.value.Attack = replacementPokemon.data.stats[1].base_stat;
            challengerTwo.value.Defense = replacementPokemon.data.stats[2].base_stat;
            challengerTwo.value.Speed = replacementPokemon.data.stats[5].base_stat;
        }
        else if (winner.value === challengerTwo.value.name) {
            let replacementPokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${replacement}`);
            challengerOne.value.image = replacementPokemon.data.sprites.other["official-artwork"].front_default
            challengerOne.value.name = replacementPokemon.data.name
            challengerOne.value.Hp = replacementPokemon.data.stats[0].base_stat;
            challengerOne.value.Attack = replacementPokemon.data.stats[1].base_stat;
            challengerOne.value.Defense = replacementPokemon.data.stats[2].base_stat;
            challengerOne.value.Speed = replacementPokemon.data.stats[5].base_stat;
        }
        else if (winner.value === "empate") {

            let replacement1 = Math.floor(Math.random() * 898) + 1;
            let replacement2 = Math.floor(Math.random() * 898) + 1;


            let replacementPokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${replacement1}`);
            challengerOne.value.image = replacementPokemon.data.sprites.other["official-artwork"].front_default
            challengerOne.value.name = replacementPokemon.data.name
            challengerOne.value.Hp = replacementPokemon.data.stats[0].base_stat;
            challengerOne.value.Attack = replacementPokemon.data.stats[1].base_stat;
            challengerOne.value.Defense = replacementPokemon.data.stats[2].base_stat;
            challengerOne.value.Speed = replacementPokemon.data.stats[5].base_stat;

            replacementPokemon = await axios.get(`https://pokeapi.co/api/v2/pokemon/${replacement2}`);
            challengerTwo.value.image = replacementPokemon.data.sprites.other["official-artwork"].front_default
            challengerTwo.value.name = replacementPokemon.data.name
            challengerTwo.value.Hp = replacementPokemon.data.stats[0].base_stat;
            challengerTwo.value.Attack = replacementPokemon.data.stats[1].base_stat;
            challengerTwo.value.Defense = replacementPokemon.data.stats[2].base_stat;
            challengerTwo.value.Speed = replacementPokemon.data.stats[5].base_stat;

        }

    }
    catch (error) {
        alert('Parece que algo salio mal en el reemplazo');
    }
    console.log(challengerOne.value.Hp);
    console.log(challengerTwo.value.Hp);
    calculatecurrentRound()
    showTextWinner.value = false

    
}

function calculatecurrentRound() {
    if (currentRound.value < rounds.value) {
        currentRound.value++

    }
    else {
        btnDisable.value = true
        endMenu()
    }
}

function endMenu() {
    if (currentRound.value >= rounds.value) {
        showBattle.value = false;
        showMenuEnd.value = true;
        console.log(winner.value);
        console.log(challengerOne.value.points);
        console.log(challengerTwo.value.points);

        if (challengerOne.value.points > challengerTwo.value.points) {
            winner.value = "equipo 1";
            console.log(winner.value);
        } else if (challengerOne.value.points < challengerTwo.value.points) {
            winner.value = "equipo 2";
            console.log(winner.value);
        } else {
            winner.value = "empate";
            console.log(winner.value);
        }
    }
}



</script>
