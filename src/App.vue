<template>
  <div id="sets" class="sets">
    <div class="set">使用预设:
      <select v-on:change="preset($event)">
        <option value="1">初级</option>
        <option value="2">中级</option>
        <option value="3">高级</option>
        <option value="4">专家</option>
        <option value="5">大师</option>
      </select>
    </div>
    <div class="set">设置行数: <input v-model="line" type="text"></div>
    <div class="set">设置列数: <input v-model="column" type="text"></div>
    <div class="set">设置雷数: <input v-model="mine" type="text"></div>
  </div>
  <button id="startbutton" v-on:click="start">开始游戏</button>
  <div id="cards" class="cards">
    <template :key="card_line" v-for="card_line in cards">
      <div v-on:touchend="touchendClick(card.i, card.j)" v-on:touchstart="touchstartClick(card.i, card.j)"
        v-on:click.left="leftClick(card.i, card.j)" @contextmenu.prevent="rightClick(card.i, card.j)" :class="'card card_state_' +
          (card.isShow ? (card.isMine ? '11' : card.nearMines) : card.state)" :key="card" v-for="card in card_line">
      </div>
    </template>
  </div>
</template>

<script>
export default {
  data() {
    return {
      line: 9,//行
      column: 9,//列
      mine: 10,//雷
      cards: [],//二维数组
      istimer: false,//是否开始计时
    };
  },
  mounted() {
  },
  methods: {
    preset(e) {
      switch (e.target.value) {
        case "1":
          this.line = 9;
          this.column = 9;
          this.mine = 10;
          break;
        case "2":
          this.line = 16;
          this.column = 16;
          this.mine = 40;
          break;
        case "3":
          this.line = 16;
          this.column = 30;
          this.mine = 99;
          break;
        case "4":
          this.line = 30;
          this.column = 30;
          this.mine = 180;
          break;
        case "5":
          this.line = 42;
          this.column = 30;
          this.mine = 230;
          break;
        default:
          this.line = 9;
          this.column = 9;
          this.mine = 10;
          break;
      }
    },
    start() {
      this.init()
    },
    //初始化
    init() {
      this.cards = [];
      document.getElementById("cards").style.gridTemplateColumns =
        "repeat(" + this.column + ", 25px)";
      document.getElementById("cards").style.gridTemplateRows =
        "repeat(" + this.line + ", 25px)";
      document.getElementById("cards").style.width =
        (this.column * 25 + 1) + "px";
      document.getElementById("sets").style.width =
        (this.column * 25 + 1) + "px";
      document.getElementById("startbutton").style.width =
        (this.column * 25 + 1) + "px";
      for (let i = 0; i < this.line; i++) {
        this.cards[i] = [];
        for (let j = 0; j < this.column; j++) {
          this.cards[i][j] = {
            i: i,
            j: j,
            isShow: false,//显示状态
            isMine: false,//是否是雷
            nearMines: 0,//周围雷数
            state: 9,//状态 
          };
        }
      }
      this.createMines();
    },
    //创建雷,并计算周围雷数
    createMines() {
      //创建雷
      if (this.mine > this.line * this.column) {
        this.mine = this.line * this.column;
      }
      if (this.mine == 0 || this.mine < 2) {
        this.mine = 2;
      };
      let nowMine = this.mine;

      while (nowMine > 0) {
        for (let i = 0; i < this.line; i++) {
          for (let j = 0; j < this.column; j++) {
            if (Math.random() > 0.8 && this.cards[i][j].isMine == false) {
              if (nowMine <= 0) break;
              this.cards[i][j].isMine = true;
              nowMine--;
              //计算周围雷数
              for (let k = -1; k < 2; k++) {
                for (let l = -1; l < 2; l++) {
                  if (
                    i + k >= 0 &&
                    j + l >= 0 &&
                    i + k < this.line &&
                    j + l < this.column
                  ) {
                    this.cards[i + k][j + l].nearMines++;
                  }
                }
              }
            }
          }
        }
      }
    },
    //左键点击
    leftClick(i, j) {
      //判断第一次点击是否为雷,如果为雷则重新初始化并且执行点击
      if (!this.firstClick()) {
        if (this.cards[i][j].isMine == true) {
          this.init();
          const a = i;
          const b = j;
          this.leftClick(a, b);
          return
        }
      }

      this.isyes();
      if (this.cards[i][j].isShow == false) {
        this.cards[i][j].isShow = true;
        //判断周围是否为0,为0则递归
        if (this.cards[i][j].nearMines == 0) {
          for (let k = -1; k < 2; k++) {
            for (let l = -1; l < 2; l++) {
              if (
                i + k >= 0 &&
                j + l >= 0 &&
                i + k < this.line &&
                j + l < this.column
              ) {
                this.leftClick(i + k, j + l);
              }
            }
          }
        }
        //判断是否雷
        if (this.cards[i][j].isMine == true) {
          //显示所有地雷
          for (let n = 0; n < this.line; n++) {
            for (let s = 0; s < this.column; s++) {
              if (this.cards[n][s].isMine == true) {
                this.cards[n][s].isShow = true;
                if (n == i && s == j) {
                  this.cards[i][j].state = 13;
                }
              }
            }
          }
          alert("你踩到雷了");
        }
      }


    },

    //右键点击
    rightClick(i, j) {
      if (this.cards[i][j].isShow == false) {
        if (this.cards[i][j].state == 9) {
          this.cards[i][j].state = 10;
        } else if (this.cards[i][j].state == 10) {
          this.cards[i][j].state = 12;
        } else {
          this.cards[i][j].state = 9;
        }
      }
    },
    //手指进入
    touchstartClick(i, j) {
      this.istimer = true;
      if (this.istimer == true) {
        setTimeout(() => {
          if (this.timeOutEvent == 0 && this.istimer == true) {
            this.istimer == false
            this.rightClick(i, j);
          }
        }, 700);
      }

    },
    //手指离开
    touchendClick() {
      this.istimer == false
    },
    //判断是否胜利
    isyes() {
      let all = this.line * this.column - this.mine - 1;
      let isyes = false;
      for (let i = 0; i < this.line; i++) {
        for (let j = 0; j < this.column; j++) {
          if (this.cards[i][j].isShow == true) {
            all--;
          }
        }
      }
      if (all == 0) {
        isyes = true;
      }
      if (isyes == true || all == 0) {
        alert("你赢了");
      }

    },

    //判断是否为第一次点击
    firstClick() {
      let show = false;
      for (let i = 0; i < this.line; i++) {
        for (let j = 0; j < this.column; j++) {
          if (this.cards[i][j].isShow == true) {
            show = true;
          }
        }
      }
      return show;
    },


  },
}
</script>

<style>
.sets {
  display: flex;
  flex-direction: row;
  justify-content: center;
  flex-wrap: wrap;
}

.set {
  width: 40%;
  display: flex;
  /* flex-direction: column; */
  justify-content: center;
  height: 32px;
  line-height: 32px;
  margin: 5px 0;
}

input,
select {
  display: block;
  width: 40%;
  height: 30px;
  margin: 2px 0;
  padding: 0;
  border: 1px solid;
  border-radius: 5px;

}

select {
  width: calc(40% + 2px);
  height: calc(32px);
}

button {
  display: block;
  margin: 0 auto;
  height: 30px;
  width: calc(100% / 1.2);
}

.cards {
  display: grid;
  /* background-color: #f0f0f0; */
  /* border: 1px solid #000; */
  /* 居中 */
  justify-content: center;
  align-items: center;
  padding: 10px;
  border-radius: 10px;
  /* width: auto; */
  margin: 0 auto;
}

.card {
  width: 25px;
  height: 25px;
  background-size: 25px;
  background-repeat: no-repeat;
}

.card_state_0 {
  background-image: url("./assets/images/0.gif");
}

.card_state_1 {
  background-image: url("./assets/images/1.gif");
}

.card_state_2 {
  background-image: url("./assets/images/2.gif");
}

.card_state_3 {
  background-image: url("./assets/images/3.gif");
}

.card_state_4 {
  background-image: url("./assets/images/4.gif");
}

.card_state_5 {
  background-image: url("./assets/images/5.gif");
}

.card_state_6 {
  background-image: url("./assets/images/6.gif");
}

.card_state_7 {
  background-image: url("./assets/images/7.gif");
}

.card_state_8 {
  background-image: url("./assets/images/8.gif");
}

.card_state_9 {
  background-image: url("./assets/images/9.gif");
}

.card_state_10 {
  background-image: url("./assets/images/10.gif");
}

.card_state_11 {
  background-image: url("./assets/images/11.gif");
}

.card_state_12 {
  background-image: url("./assets/images/12.gif");
}

.card_state_13 {
  background-image: url("./assets/images/13.gif");
}
</style>