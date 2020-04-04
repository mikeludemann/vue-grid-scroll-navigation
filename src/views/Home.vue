<template>
	<div class="home">
		<img alt="Vue logo" src="../assets/logo.png">
		<HelloWorld msg="Welcome to Your Vue.js App"/>
		<GridScrollNavigation :colors='{
      0: "#867100",
      1: "#7F4200",
      2: "#99813D",
      3: "#40FEFF",
      4: "#14CC99",
      5: "#00BAFF",
      6: "#0082B2",
      7: "#B25D7A",
      8: "#00FF17",
      9: "#006B49",
      10: "#00B27A",
      11: "#996B3D",
      12: "#CC7014",
      13: "#40FF8C",
      14: "#FF3400",
      15: "#ECBB5E",
      16: "#ECBB0C",
      17: "#B9D912",
      18: "#253A93",
      19: "#125FB9",
    }'
		:dataList='[
      { id: 1, linkID: "fish", status: true, name: "fish" },
      { id: 2, linkID: "meat", status: false, name: "meat" },
      { id: 3, linkID: "salad", status: false, name: "salad" },
      { id: 4, linkID: "duck", status: false, name: "duck" },
      { id: 5, linkID: "chicken", status: false, name: "chicken" },
      { id: 6, linkID: "shake", status: false, name: "shake" },
      { id: 7, linkID: "tea", status: false, name: "tea" },
      { id: 8, linkID: "coffee", status: false, name: "coffee" },
      { id: 9, linkID: "muesli", status: false, name: "fish" },
      { id: 10, linkID: "seafood", status: false, name: "seafood" },
      { id: 11, linkID: "bread", status: false, name: "bread" },
      { id: 12, linkID: "burger", status: false, name: "burger" },
      { id: 13, linkID: "icecream", status: false, name: "ice cream" },
      { id: 14, linkID: "donuts", status: false, name: "donuts" },
      { id: 15, linkID: "rice", status: false, name: "rice" }
    ]'/>
	</div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue';
import GridScrollNavigation from '@/components/grid-scroll-navigation.vue';

export default {
	name: 'Home',
	components: {
		HelloWorld,
		GridScrollNavigation
	}
}
</script>
