<template>
    <div id="app">
        <h1>Welcome to Tower of Hanoi</h1>
        <p>Move all the disks over to Tower 3</p>
        <p>Remember - you cannot place a larger disk onto a smaller disk.</p>
        <h2>Use your arrow keys(up and down, left and right) </h2>
        <div class="towers">

            <div v-for="(tower, key) in towers" :key="key"
                 :class="getClass(key)">
                <div class="tower__up">

                    <div class="circle" v-for="item in tower.children"
                         v-bind:key="item.width"
                         v-bind:style="{width: item.width+'px'}"></div>

                </div>
                <div class="tower__base"></div>
            </div>


        </div>

        <div v-if="success" class="img-wrap">
            <img src="https://www.meme-arsenal.com/memes/c5ecb681f153a68857fa3eda4e02aaf1.jpg" alt="" class="img-success">
            <div class="success-title">You did it!</div>
            <button class="success-btn" @click="reset">Start again</button>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'App',
        data() {
            return {
                towers: [
                    {
                        children: [{width: 70}, {width: 100}, {width: 130}, {width: 160}, {width: 190}]
                    },
                    {
                        children: []
                    },
                    {
                        children: []
                    }
                ],
                activeTower: 0,
                chosenTower: '',
                success: false
            }
        },
        mounted() {
            document.onkeydown = this.keydown;
        },
        methods: {
            keydown: function (e) {
                if (e.keyCode === 37 || e.keyCode === 100) { //left
                    this.keyLeft()
                } else if (e.keyCode === 39 || e.keyCode === 102) { //right
                    this.keyRight()
                } else if (e.keyCode === 38 || e.keyCode === 104) { //up
                    if (this.towers[this.activeTower].children.length) {
                        this.keyUp();
                    }
                } else if (e.keyCode === 40 || e.keyCode === 98) { //down
                    if (this.chosenTower !== '') {
                        this.keyBottom()
                    }

                }
            },
            keyLeft() {
                if (this.activeTower !== 0) {
                    this.activeTower--;
                } else {
                    this.activeTower = this.towers.length - 1;
                }

            },
            keyRight() {
                if (this.activeTower !== this.towers.length - 1) {
                    this.activeTower++;
                } else {
                    this.activeTower = 0;
                }
            },
            keyUp() {
                this.chosenTower = this.activeTower;
            },
            keyBottom() {

                let tower = this.towers[this.chosenTower].children;
                let circle = tower[0];

                if (this.checkIfLess(circle.width)) {
                    this.towers[this.activeTower].children.unshift(circle);
                    tower.splice(0, 1);
                    this.chosenTower = '';
                    this.ifSuccesful()
                } else {
                    let circleActive = document.querySelector('.tower.chosen .circle');

                    circleActive.classList.add('error');
                    setInterval(() => circleActive.classList.remove('error'), 1000);
                }

            },
            getClass(key) {
                let className = 'tower';
                if (this.activeTower === key) {
                    className += ' active';
                }
                if (this.chosenTower === key) {
                    className += ' chosen';
                }

                if (this.chosenTower === key && this.activeTower !== key) {
                    let walkingCircle = document.querySelector(`.tower:nth-child(${this.chosenTower + 1}) .circle`);
                    className += ' walking-circle';
                    let towerActive = document.querySelector(`.tower:nth-child(${this.activeTower + 1})`);
                    let centerCoords = towerActive.offsetLeft + towerActive.offsetWidth / 2 - walkingCircle.offsetWidth / 2;
                    walkingCircle.style.left = centerCoords + 'px';

                } else if (this.chosenTower === key) {
                    let walkingCircle = document.querySelector(`.tower:nth-child(${this.chosenTower + 1}) .circle`);
                    walkingCircle.style.left = '';
                }
                return className;
            },
            ifSuccesful() {
                if (this.towers[0].children.length || this.towers[1].children.length) {
                    return false
                }
                let array = this.towers[2];


                    for (let j = 0; j < array.length - 1; j++) {
                        let childCurrent = array.children[j].width;
                        let childNext = array.children[j + 1].width;
                        if (childCurrent > childNext) {
                            return false
                        }

                    }


                this.success = true;
                return true;

            },
            checkIfLess(newItem) {

                if (!this.towers[this.activeTower].children.length) {
                    return true
                }
                let firstItem = this.towers[this.activeTower].children[0].width;
                return newItem <= firstItem;
            },
            reset(){
                this.towers =  [
                    {
                        children: [{width: 70}, {width: 90}, {width: 110}, {width: 130}, {width: 150}]
                    },
                    {
                        children: []
                    },
                    {
                        children: []
                    }
                ];
                this.activeTower = 0;
                this.chosenTower = '';
                this.success = false;
            }
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        max-width: 1300px;
        margin: 60px auto;
    }

    .towers {
        padding: 120px 0 0;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-gap: 20px;
        margin: 0 auto;
        width: max-content;
    }

    .tower {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .active .tower__base,
    .active .tower__up:before {
        background: #888888;
    }

    .chosen .tower__base,
    .chosen .tower__up:before {
        background: pink;
    }

    .chosen .circle:first-child {
        position: fixed;
        top: 227px;
    }

    .walking-circle .circle:first-child {
        position: fixed;
        top: 227px;

    }

    .tower__base {
        background: black;
        border-radius: 20px;
        width: 200px;
        height: 30px;
        transition: all .1s ease-in-out;
    }

    .tower__up {
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
        align-items: center;
        position: relative;
        height: 180px;
        width: 100%;
    }

    .tower__up:before {
        content: "";
        display: block;
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        top: 0;
        height: 100%;
        width: 30px;
        background: black;
        border-radius: 20px 20px 0 0;
        transition: all .1s ease-in-out;

    }

    .circle {
        background: white;
        border-radius: 20px;
        height: 25px;
        width: 100%;
        border: 5px solid grey;
        z-index: 2;

    }

    .circle.error {
        border-color: red;
        animation: 1s ease 0s error;
    }

    @keyframes error {

        0% {
            transform: translateX(-10px);
        }
        20% {
              transform: translateX(10px);
          }
        40% {
            transform: translateX(-10px);
        }
        60% {
            transform: translateX(10px);
        }
        80% {
            transform: translateX(-10px);
        }
    }
    .img-wrap{
        padding: 50px 10px;
       position: fixed;
        background: dimgrey;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        z-index: 3;
    }
    .img-success{
        max-width: 500px;
        height: auto;
    }
    .success-title{
        margin-top: 30px;
        font-size: 30px;
        color: white;
    }
    .success-btn{
        outline: 0;
        border: none;
        background: #2c3e50;
        color: white;
        text-align: center;
        padding: 20px;
        border-radius: 20px;
        margin-top: 20px;
        font-size: 20px;
        cursor: pointer;
    }
</style>
