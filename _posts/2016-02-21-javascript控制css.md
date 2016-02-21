---
layout: post
title: javascript操纵css
categories: [语言学习]
tags: [html, css]
---
####动态样式
1. javascript和css结合，可以动态改变html页面元素内容和样式，利用innerHTML属性，用来设置或获取位于对象起始和结束标记内的HTML
2. javascript利用styleSheets属性，以数组的形式获取当前网页的样式集合，rules表示第几个选择器，属性cssRules表示四第几条规则。
	
	```
	function fnInit() {
		var oStyleSheet = document.styleSheets[0]; //css集合
		var oRule = oStyleSheet.rules[0]; //第一个选择器
		onRule.style.width="200px";
		
	}
	```
3. 动态定位, pixelLeft属性返回定位元素左边界偏移量的整数像素值，pixelTop类似。以下代码在safari里有效果
	
	```
	<!DOCTYPE html>
<html>
	<head>
		<style type="text/css">
			#d1{
				position: absolute;
				width: 300px;
				height: 300px;
				visibility: visible;
				color: #fff;
				background: #555;
			}
			#d2 {
				position: absolute;
				width: 300px;
				height: 300px;
				visibility: visible;
				color: #fff;
				background: red;
			}
			#d3{
				position: absolute;
				width: 150px;
				height: 150px;
				visibility: visible;
				color: #fff;
				background: blue;
			}
		</style>
		<script type="text/javascript">
			var d1, d2, d3, w, h;
			window.onload = function  () {
				d1 = document.getElementById('d1');
				d2 = document.getElementById('d2');
				d3 = document.getElementById('d3');
				w = window.innerWidth;
				h = window.innerHeight;
			}
			function divMoveTo (d, x, y) {
				d.style.pixelLeft = x;
				d.style.pixelTop = y;
			}
			function divMoveBy (d, dx, dy) {
				d.style.pixelTop += dy;
				d.style.pixelLeft += dx;
			}
		</script>
	</head>
	<body id="bodyId">
		<form name="form1">
			<h3>移动定位</h3>
			<p>
				<input type="button" value="移动d2" onclick="divMoveBy(d2, 100,100)">
				<input type="button" value="移动d3到d2(0,0)" onclick="divMoveTo(d3, 0, 0)">
				<input type="button" value="移动d3到d2(75,75)" onclick="divMoveTo(d3, 75,75)">
			</p>
		</form>
		<div id="d1">
			<b>d1</b>
		</div>
		<div id="d2">
			<b>d2</b><br><br>
			d2包含d3
		<div id="d3">
			<b>d3</b><br><br>
			d3是d2的子层
		</div>
		</div>
	</body>
</html>
	```
4. 显示与隐藏
	
	```
	target = document.getElementById(targetId);
	target.style.display = "none";
	target.style.display = "none";
	target.style.display = "block";
	```
