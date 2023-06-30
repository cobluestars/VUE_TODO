<template>
  <img alt="Vue logo" src="./assets/catandme.png" width="500" height="300">
  <div>
    <h1>
      안녕하세요, 연봉 {{ earn }}억 원, 인센티브 {{ incentive }}%의 {{ name }} 님,
    </h1>
    <p class="study">Vue 공부 중입니다...</p>
    <p class="study">{{secret(",")}} 2023 네이버 기술직군 프론트엔드 신입 목표로 공부 중입니다...</p>
    <p class="study">{{ greeting("합시다. 할 수 있다!") }}</p>
    <button class="btn btn-info" v-on:click= "earn1(); updateName()">민초맛 버튼(덧셈)</button>
    <button class="btn btn-warning" v-on:click= "incentive1();">오렌지맛 버튼(곱셈)</button>
    <button class="btn btn-danger" v-on:click= "reset();">딸기맛 버튼(초기화)</button>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const name = ref("Min-woo Kim");
    const earn = ref(1);
    const incentive = ref(100);

    function earn1() {
      earn.value++;
    }

    function incentive1() {
      incentive.value*=2;
    }

    function reset() {
      incentive.value = 100;
      earn.value = 1;
    }

    function updateName() {
      console.log(name);

      if (name.value === "Min-woo Kim") {
      name.value = "김민우";
      } else {
      name.value = "Min-woo Kim";
      }
    }

    function greeting(message) {
      return "화이팅 " + message;
    }

    const greet = greeting(name);
    console.log(greet);

    const secret = (message) => {
      return "mwkim은" + message;
    }

    return {
       name, greeting, secret, updateName, earn, earn1, incentive, incentive1, reset
      }; //Vue에서는 무조건 return에 함수와 객체를 기입해 주어야 한다.
  },
};

</script>

<style>
* {
 background-color: black; 
}
div {
  text-align: center;
  margin-top: 60px;
  animation: rainbow 2.5s linear;
	animation-iteration-count: infinite;
}
button {
  margin: 10px;
}
.study{
  font-weight: bold;
  font-size: 20px;
  animation: rainbow 2.5s linear;
	animation-iteration-count: infinite;
}

@keyframes rainbow{
		100%,0%{
			color: rgb(255,0,0);
		}
		8%{
			color: rgb(255,127,0);
		}
		16%{
			color: rgb(255,255,0);
		}
		25%{
			color: rgb(127,255,0);
		}
		33%{
			color: rgb(0,255,0);
		}
		41%{
			color: rgb(0,255,127);
		}
		50%{
			color: rgb(0,255,255);
		}
		58%{
			color: rgb(0,127,255);
		}
		66%{
			color: rgb(0,0,255);
		}
		75%{
			color: rgb(127,0,255);
		}
		83%{
			color: rgb(255,0,255);
		}
		91%{
			color: rgb(255,0,127);
		}
}
</style>
