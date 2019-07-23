<template>
    <v-layout row wrap>

        <v-flex xs3 offset-xs1>
                <div class="small-5 columns text-center">
                    <img 
                        :src="myChoiceImg" 
                        alt="" 
                        class="text-center"
                    >
                    <h1 class="text-center"><strong>YOU</strong></h1>
                    <div class="battle-wrap">
                        <img
                            v-for="(life, index) in lifeOfMe"
                            :key="index"
                            src="../assets/images/heart.jpg"
                            class="heart"
                            alt=""
                        >
                        <img
                            v-for="(life, index) in leftLifeOfMe"
                            :key="index"
                            src="../assets/images/broken-heart.jpg"
                            class="heart"
                            alt=""
                        >
                        <div class="small-8 small-offset-2 columns text-center">
                            <label 
                                v-for="(select, index) in selects"
                                :key="index"
                                class="radio-label"
                            >
                                <input type="radio" v-model="myChoice" :value="select.value"> {{ select.name }}
                            </label>
                        </div>                        
                    </div>                    
                </div>
        </v-flex>

        <v-flex xs3 offset-xs1>
                <div class="small-2 columns text-center">
                    <h1 style="font-size:100px;"><strong>{{ count }}</strong></h1>
                    <div class="small-12 columns">
                        <div class="text-center" v-if="isSelectable">
                            <button class="start-btn" @click="startGame()">선택 완료!</button>
                        </div>
                        <div v-else class="loading"> 기다리는 중...</div>
                    </div>                    
                </div>

        </v-flex>

        
        <v-flex xs3 offset-xs1>
                <div class="small-5 columns text-center">
                    <img 
                        :src="comChoiceImg" 
                        alt="" 
                        class="text-center"
                    >
                    <h1 class="text-center"><strong>Computer</strong></h1><div class="battle-wrap">
                        <img
                            v-for="(life, index) in lifeOfCom" 
                            :key="index"
                            src="../assets/images/heart.jpg"
                            class="heart"
                            alt=""
                        >
                        <img
                            v-for="(life, index) in leftLifeOfCom"
                            :key="index"
                            src="../assets/images/broken-heart.jpg"
                            class="heart"
                            alt=""
                        >
                        <div class="small-6 columns text-center">
                            <p>생각 중...</p>
                        </div>                        
                    </div>
                </div>
        </v-flex>



        <v-flex xs12 >
            <div class="small-12 columns log">
                <ul>
                    <li 
                        :class="{
                            'win-log': log.winner === 'me', 
                            'defeat-log': log.winner === 'com', 
                            'draw-log': log.winner === 'no one'
                        }"
                        v-for="(log, index) in logs"
                        :key="index"
                    >
                        {{ log.messege }}
                    </li>
                </ul>
            </div>
        </v-flex>

    </v-layout>
   
</template>



<style scoped>
.text-center {
    text-align: center;
}

.battle-wrap {
    max-width:300px;
    margin:0 auto;
}

.heart {
    display:inline-block;
    width:30%;
}

.start-btn {
    color: blue;
    padding:15px 40px;
    margin-top:30px;
    background-color: #e4e8ff;
    transition: 0.1s color ease-in-out, 0.1s background-color ease-in-out;
}

.start-btn:hover {
    color:#fff;
    background-color: blue;
}
.loading {
    color: grey;
    display:inline-block;
    padding:15px 40px;
    margin-top:30px;
    background-color: #dadada;
    transition: 0.1s color ease-in-out, 0.1s background-color ease-in-out;
}

.radio-label{
    padding-left:10px;
    padding-right:10px;
}

.log {
    width:100%;
    text-align:center;
}
.log ul {
    list-style: none;
    font-weight: bold;
    text-transform: uppercase;
}

.log ul li {
    margin: 5px;
    padding:10px;
    border:1px solid #dadada;
}

.win-log {
    color:green;
    background-color:#96edc6;
}
.defeat-log {
    color: red;
    background-color: #ffc0c1;
}
.draw-log {
    background-color: #f7f7f7;
}
</style>


<script>
export default {
    data() {
        return {
            test: "making game... test!",
            myChoice: null,
            comChoice: null,
            count: 3,
            winner: null,
            lifeOfMe: 3,
            lifeOfCom: 3,
            isSelectable: true,
            logs:[],
            selects:[
                { name: '가위', value: 'scissor'},
                { name: '바위', value: 'rock'},
                { name: '보', value: 'paper'},
            ]            
        }
    },
    computed: {
        myChoiceImg: function () {
            return this.myChoice !== null ? `../assets/images/${this.myChoice}.jpg` : '../assets/images/question.jpg'
        },
        comChoiceImg: function () {
            return this.comChoice !== null ? `../assets/images/${this.comChoice}.jpg` : '../assets/images/question.jpg'
        },
        leftLifeOfMe: function () {
            return 3 - this.lifeOfMe
        },
        leftLifeOfCom: function () {
            return 3 - this.lifeOfCom
        }
    },
    watch: {
        count: function (newVal) {
            if(newVal === 0){
                // 컴퓨터가 가위바위보를 선택하는 
                this.selectCom()
                
                // 가위바위보 승패 결정 & 몫을 차감
                this.whoIsWin()

                // 게임 리셋
                this.count = 3
                this.isSelectable = true

                // 로그를 업데이트 하는 부분
                this.updateLogs()
            }
        },
        lifeOfMe: function(newVal) {
            if(newVal === 0){
                // 게임을 종료
                this.endGame('안타깝네요. 당신이 패배하였습니다.')
            }
        },
        lifeOfCom: function(newVal) {
            if(newVal === 0){
                this.endGame('축하드립니다. 당신이 승리하였습니다.')
            }
        }
    },
    methods: {
        startGame: function () {
            // 버튼이 보이지 않음
            this.isSelectable = false
            if(this.myChoice === null){
                alert('가위 바위 보 중 하나를 선택해주세요.')
                this.isSelectable = true
            } else {
                let countDown = setInterval(()=> {
                    this.count --
                    if(this.count === 0){
                        clearInterval(countDown)
                    }
                }, 1000)
            }
        },
        selectCom: function () {
            let number = Math.random()
                if(number < 0.33){
                    this.comChoice = 'scissor'
                } else if(number < 0.66){
                    this.comChoice = 'rock'
                } else {
                    this.comChoice = 'paper'
                }
        },
        whoIsWin: function () {
            if(this.myChoice === this.comChoice) this.winner = 'no one'
            else if(this.myChoice === 'rock' &&  this.comChoice === 'scissor') this.winner = 'me'
            else if(this.myChoice === 'scissor' &&  this.comChoice === 'paper') this.winner = 'me'
            else if(this.myChoice === 'paper' &&  this.comChoice === 'rock') this.winner = 'me'
            else if(this.myChoice === 'scissor' &&  this.comChoice === 'rock') this.winner = 'com'
            else if(this.myChoice === 'paper' &&  this.comChoice === 'scissor') this.winner = 'com'
            else if(this.myChoice === 'rock' &&  this.comChoice === 'paper') this.winner = 'com'
            else this.winner = 'error'

            // 몫 차감
            if(this.winner === 'me') {
                this.lifeOfCom --
            }
            else if(this.winner === 'com'){
                this.lifeOfMe --
            }
        },
        updateLogs: function () {
            let log = {
                messege: `You: ${this.myChoice}, Computer: ${this.comChoice}`,
                winner: this.winner
            }
            
            this.logs.unshift(log)
        },
        endGame: function (msg) {
            setTimeout(() => {
                confirm(msg)
                this.lifeOfMe = 3
                this.lifeOfCom = 3
                this.myChoice = null
                this.comChoice = null
                this.winner = null
                this.logs =[]
            }, 500)
        }
    }
}
</script>
