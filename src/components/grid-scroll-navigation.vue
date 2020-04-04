<template lang="html">

	<section class="grid-scroll-navigation">
		<div class="nav--wrapper">
			<nav id="navi" class="navi--context">
				<div id="naviContent" class="navi--contents">
					<a v-for="data in dataList" :key="id" :href="'#' + data.linkID" class="navi--link" :aria-selected="data.status">
						{{ data.name }}
					</a>
					<span id="indicator" class="navi--indicator"></span>
				</div>
			</nav>
			<button id="leftMove" class="move--setup left--move" type="button">
				<svg class="arrow--icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 551 1024"><path d="M445.44 38.183L-2.53 512l447.97 473.817 85.857-81.173-409.6-433.23v81.172l409.6-433.23L445.44 38.18z"/></svg>
			</button>
			<button id="rightMove" class="move--setup right--move" type="button">
				<svg class="arrow--icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 551 1024"><path d="M105.56 985.817L553.53 512 105.56 38.183l-85.857 81.173 409.6 433.23v-81.172l-409.6 433.23 85.856 81.174z"/></svg>
			</button>
		</div>
	</section>

</template>

<script lang="js">

	export default {
		name: 'GridScrollNavigation',
		props: {
			dataList: {
				type: Array
			},
			colors: {
				type: Object
			}
		},
		mounted () {
			var SETTINGS = {
				navBarTravelling: false,
				navBarTravelDirection: "",
				navBarTravelDistance: 150
			}

			var colours = this.colors;

			document.documentElement.classList.remove("no-js");
			document.documentElement.classList.add("js");

			var leftMove = document.getElementById("leftMove");
			var rightMove = document.getElementById("rightMove");

			var indicator = document.getElementById("indicator");

			var navi = document.getElementById("navi");
			var naviContent = document.getElementById("naviContent");

			navi.setAttribute("data-overflowing", determineOverflow(naviContent, navi));

			moveIndicator(navi.querySelector("[aria-selected=\"true\"]"), colours[0]);

			var last_known_scroll_position = 0;
			var ticking = false;

			function doSomething(scroll_pos) {

				navi.setAttribute("data-overflowing", determineOverflow(naviContent, navi));

			}

			navi.addEventListener("scroll", function() {

				last_known_scroll_position = window.scrollY;

				if (!ticking) {
					window.requestAnimationFrame(function() {
						doSomething(last_known_scroll_position);
						ticking = false;
					});
				}

				ticking = true;

			});


			leftMove.addEventListener("click", function() {

				if (SETTINGS.navBarTravelling === true) {

					return;

				}

				if (determineOverflow(naviContent, navi) === "left" || determineOverflow(naviContent, navi) === "both") {

					var availableScrollLeft = navi.scrollLeft;

					if (availableScrollLeft < SETTINGS.navBarTravelDistance * 2) {

						naviContent.style.transform = "translateX(" + availableScrollLeft + "px)";

					} else {

						naviContent.style.transform = "translateX(" + SETTINGS.navBarTravelDistance + "px)";

					}

					naviContent.classList.remove("navi--contents--no--transition");

					SETTINGS.navBarTravelDirection = "left";
					SETTINGS.navBarTravelling = true;

				}

				navi.setAttribute("data-overflowing", determineOverflow(naviContent, navi));

			});

			rightMove.addEventListener("click", function() {

				if (SETTINGS.navBarTravelling === true) {
					return;
				}

				if (determineOverflow(naviContent, navi) === "right" || determineOverflow(naviContent, navi) === "both") {

					var navBarRightEdge = naviContent.getBoundingClientRect().right;
					var navBarScrollerRightEdge = navi.getBoundingClientRect().right;

					var availableScrollRight = Math.floor(navBarRightEdge - navBarScrollerRightEdge);

					if (availableScrollRight < SETTINGS.navBarTravelDistance * 2) {
						naviContent.style.transform = "translateX(-" + availableScrollRight + "px)";
					} else {
						naviContent.style.transform = "translateX(-" + SETTINGS.navBarTravelDistance + "px)";
					}

					naviContent.classList.remove("navi--contents--no--transition");

					SETTINGS.navBarTravelDirection = "right";
					SETTINGS.navBarTravelling = true;

				}

				navi.setAttribute("data-overflowing", determineOverflow(naviContent, navi));

			});

			naviContent.addEventListener(
				"transitionend",
				function() {

					var styleOfTransform = window.getComputedStyle(naviContent, null);
					var tr = styleOfTransform.getPropertyValue("-webkit-transform") || styleOfTransform.getPropertyValue("transform");

					var amount = Math.abs(parseInt(tr.split(",")[4]) || 0);
					naviContent.style.transform = "none";
					naviContent.classList.add("navi--contents--no--transition");

					if (SETTINGS.navBarTravelDirection === "left") {
						navi.scrollLeft = navi.scrollLeft - amount;
					} else {
						navi.scrollLeft = navi.scrollLeft + amount;
					}
					SETTINGS.navBarTravelling = false;
				},
				false
			);

			naviContent.addEventListener("click", function(e) {

				var links = [].slice.call(document.querySelectorAll(".navi--link"));

				links.forEach(function(item) {

					item.setAttribute("aria-selected", "false");

				})

				e.target.setAttribute("aria-selected", "true");


				moveIndicator(e.target, colours[links.indexOf(e.target)]);

			});

			function moveIndicator(item, color) {

				var textPosition = item.getBoundingClientRect();
				var container = naviContent.getBoundingClientRect().left;
				var distance = textPosition.left - container;
				var scroll = naviContent.scrollLeft;

				indicator.style.transform = "translateX(" + (distance + scroll) + "px) scaleX(" + textPosition.width * 0.01 + ")";

				if (color) {

					indicator.style.backgroundColor = color;

				}
			}

			function determineOverflow(content, container) {

				var containerMetrics = container.getBoundingClientRect();
				var containerMetricsRight = Math.floor(containerMetrics.right);
				var containerMetricsLeft = Math.floor(containerMetrics.left);
				var contentMetrics = content.getBoundingClientRect();
				var contentMetricsRight = Math.floor(contentMetrics.right);
				var contentMetricsLeft = Math.floor(contentMetrics.left);

				if (containerMetricsLeft > contentMetricsLeft && containerMetricsRight < contentMetricsRight) {

					return "both";

				} else if (contentMetricsLeft < containerMetricsLeft) {

					return "left";

				} else if (contentMetricsRight > containerMetricsRight) {

					return "right";

				} else {

					return "none";

				}

			}

			/**
			* dragscroll - scroll area by dragging
			*/

			var define;

			(function (root, factory) {
				if (typeof define === 'function' && define.amd) {
					define(['exports'], factory);
				} else if (typeof exports !== 'undefined') {
					factory(exports);
				} else {
					factory((root.dragscroll = {}));
				}
			}(this, function (exports) {
				var _window = window;
				var _document = document;
				var mousemove = 'mousemove';
				var mouseup = 'mouseup';
				var mousedown = 'mousedown';
				var EventListener = 'EventListener';
				var addEventListener = 'add'+EventListener;
				var removeEventListener = 'remove'+EventListener;
				var newScrollX, newScrollY;

				var dragged = [];
				var reset = function(i, el) {
					for (i = 0; i < dragged.length;) {
						el = dragged[i++];
						el = el.container || el;
						el[removeEventListener](mousedown, el.md, 0);
						_window[removeEventListener](mouseup, el.mu, 0);
						_window[removeEventListener](mousemove, el.mm, 0);
					}

					dragged = [].slice.call(_document.getElementsByClassName('dragscroll'));

					for (i = 0; i < dragged.length;) {
						(function(el, lastClientX, lastClientY, pushed, scroller, cont){
							(cont = el.container || el)[addEventListener](
								mousedown,
								cont.md = function(e) {
									if (!el.hasAttribute('nochilddrag') ||
										_document.elementFromPoint(
											e.pageX, e.pageY
										) === cont
									) {
										pushed = 1;
										lastClientX = e.clientX;
										lastClientY = e.clientY;

										e.preventDefault();
									}
								}, 0
							);

							_window[addEventListener](
								mouseup, cont.mu = function() {pushed = 0;}, 0
							);

							_window[addEventListener](
								mousemove,
								cont.mm = function(e) {
									if (pushed) {
										(scroller = el.scroller||el).scrollLeft -=
											newScrollX = (- lastClientX + (lastClientX=e.clientX));
										scroller.scrollTop -=
											newScrollY = (- lastClientY + (lastClientY=e.clientY));
										if (el === _document.body) {
											(scroller = _document.documentElement).scrollLeft -= newScrollX;
											scroller.scrollTop -= newScrollY;
										}
									}
								}, 0
							);
						})(dragged[i++]);
					}
				}

				if (_document.readyState === 'complete') {
					reset();
				} else {
					_window[addEventListener]('load', reset, 0);
				}

				exports.reset = reset;
			}));
		},
		data () {
			return {

			}
		},
		methods: {

		},
		computed: {

		}
}


</script>

<style scoped lang="scss">
.grid-scroll-navigation {

}

* {
	box-sizing: inherit;
}

.nav--wrapper {
	position: relative;
	padding: 0 11px;
	box-sizing: border-box;
}

.navi--context {
	overflow-x: auto;
	overflow-y: hidden;
	-webkit-overflow-scrolling: touch;
	white-space: nowrap;
	position: relative;
	font-size: 0;
}
.js .navi--context {
	-ms-overflow-style: -ms-autohiding-scrollbar;
}

.js .navi--context::-webkit-scrollbar {
	display: none;
}

.navi--contents {
	float: left;
	transition: -webkit-transform .2s ease-in-out;
	transition: transform .2s ease-in-out;
	transition: transform .2s ease-in-out, -webkit-transform .2s ease-in-out;
	position: relative;
}

.navi--contents--no--transition {
	transition: none;
}

.navi--link {
	text-decoration: none;
	color: #888;
	font-size: 1.2rem;
	font-family: -apple-system, sans-serif;
	display: inline-flex;
	align-items: center;
	border: 1px solid transparent;
	padding: 0 11px;
	min-height: 100px;
	min-width: 100px;
}

.navi--link + .navi--link {
	border-left-color: #eee;
}

.navi--link[aria-selected="true"] {
	color: #111;
}

.move--setup {
	-webkit-appearance: none;
	-moz-appearance: none;
	appearance: none;
	background: transparent;
	padding: 0;
	border: 0;
	position: absolute;
	top: 0;
	bottom: 0;
	opacity: 0;
	transition: opacity .3s;
}

.move--setup:focus {
	outline: 0;
}

.move--setup:hover {
	cursor: pointer;
}

.left--move {
	left: 0;
}

[data-overflowing="both"] ~ .left--move, [data-overflowing="left"] ~ .left--move {
	opacity: 1;
}

.right--move {
	right: 0;
}

[data-overflowing="both"] ~ .right--move, [data-overflowing="right"] ~ .right--move {
	opacity: 1;
}

.arrow--icon {
	width: 20px;
	height: 44px;
	fill: #bbb;
}

.navi--indicator {
	position: absolute;
	bottom: 0;
	left: 0;
	height: 4px;
	width: 100px;
	background-color: transparent;
	-webkit-transform-origin: 0 0;
	transform-origin: 0 0;
	transition: background-color .2s ease-in-out, -webkit-transform .2s ease-in-out;
	transition: transform .2s ease-in-out, background-color .2s ease-in-out;
	transition: transform .2s ease-in-out, background-color .2s ease-in-out, -webkit-transform .2s ease-in-out;
}
</style>
