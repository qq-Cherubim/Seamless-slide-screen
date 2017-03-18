# Seamless-slide-screen
无缝滑屏——移动端


<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<meta name="viewport" content="width=device-width,initial-scale=1" />
		<title></title>
		<style type="text/css" id="css">
			*{
				margin: 0;
				padding: 0;
			}
			html,body{
				height: 100%;
				overflow: hidden;
			}
			#wrap{
				position: relative;
			}
			#list{
				position: absolute;
				list-style: none;
				overflow: hidden;
				/*width: 500%;*/
			}
			#list li{
				float: left;
				/*width: 20%;*/
			}
			
			#list li img{
				display: block;
				width: 100%;
			}
			#nav{
				position: absolute;
				left: 0;
				bottom: 10px;
				width: 100%;
				height: 10px;				
				text-align: center;
			}
			#nav span{
				width: 10px;
				height: 10px;
				border-radius: 5px;
				display: inline-block;
				background: gray;
			}
			#nav .active{
				background: deeppink;
			}
		</style>
		<!--
			position: absolute;
			left值，进行滑屏
			
			这时候对于PC端，影响不大
			
			但是对于移动端,每滑一次屏幕，加载一下position: absolute;left值
			移动端浏览器内存小，对于用户来说，费流量
			
			
		-->
		
	</head>
	<body>
		<div id="wrap">
			<ul id="list">
				<li>
					<a href="###">
						<img src="img/1.jpg"/>
					</a>
				</li>
				<li>
					<a href="###">
						<img src="img/2.jpg"/>
					</a>
				</li>
				<li>
					<a href="###">
						<img src="img/3.jpg"/>
					</a>
				</li>
				<li>
					<a href="###">
						<img src="img/4.jpg"/>
					</a>
				</li>
				<li>
					<a href="###">
						<img src="img/5.jpg"/>
					</a>
				</li>
			</ul>
			<div id="nav">
				<span class="active"></span>
				<span></span>
				<span></span>
				<span></span>
				<span></span>
			</div>
		</div>
		<!--
			如果用定位实现，ul整体在移动
		-->
	</body>
	<script type="text/javascript" src="js/transform.js"></script>
	<script type="text/javascript">
		//取消事件默认行为
		document.addEventListener('touchstart',function(ev){
			ev.preventDefault();
		});
		
		window.onload = function(){
			//获取元素
			var wrap = document.getElementById('wrap');
			var list = document.getElementById('list');
			list.innerHTML += list.innerHTML;
			
			var lis = document.querySelectorAll('#list li');
			var spanNodes = document.querySelectorAll('#nav span');
			
			//布局样式
			var css = document.getElementById('css');
			var cssText = '#list{width: '+ lis.length +'00%;}';
			cssText += '#list li{width: '+ (100/lis.length) +'%;}'
			cssText += '#wrap{height: '+ lis[0].offsetHeight +'px;}'
			css.innerHTML += cssText;
			
			//滑屏处理
			
			//定义手指和元素初始位置
			var startX = 0;
			var elementX = 0;
			//给每一个图片定义一个下标
			var now = 0;
			//定义一个变量用来保存2d变换产生的值
			//var translateX = 0;
			//定时器名字
			var clearTime = 0;
			
			
			wrap.addEventListener('touchstart',function(ev){
				var touch = ev.changedTouches[0];
				
				//关定时器
				clearInterval(clearTime);
				
				list.style.transition = 'none';
				
				//无缝
				var left = tranformCss(list,'translateX');
				now = Math.round(-left/document.documentElement.clientWidth);
				
				if(now == 0){
					now = spanNodes.length;
				}else if(now == lis.length-1){
					now = spanNodes.length -1;
				}
				
				tranformCss(list,'translateX',-now * document.documentElement.clientWidth);
				
				startX = touch.clientX;
				//elementX = list.offsetLeft;
				//elementX = translateX;
				elementX = tranformCss(list,'translateX');	
				
			});
			
			wrap.addEventListener('touchmove',function(ev){
				var touch = ev.changedTouches[0];
				
				
				var nowX = touch.clientX;
				//手指位移的差值
				var disX = nowX - startX;
				
				//translateX = elementX + disX;
				//list.style.left = elementX + disX + 'px';
				//list.style.transform = 'translateX('+ translateX +'px)';
				tranformCss(list,'translateX',elementX + disX);
			});
			
			wrap.addEventListener('touchend',function(ev){
				var touch = ev.changedTouches[0];
								
				//var left = list.offsetLeft;
				//var left = translateX;
				var left = tranformCss(list,'translateX');
				
				//确定now
				//如果手指向左滑，disX <0,left减小,now增加
//				if(disX <0){
//					now = Math.ceil(-left/document.documentElement.clientWidth);
//				}else{
//					//如果手指向右滑，disX >0,left增加,now减小
//					now = Math.floor(-left/document.documentElement.clientWidth); 
//				}


				now = Math.round(-left/document.documentElement.clientWidth);
				
				//判断范围，不让它跳转到空白区域
				if(now<0){
					now = 0;
				}else if(now > lis.length-1){
					 now = lis.length-1;
				};
				
				list.style.transition = '0.5s';
				//ul的偏移量
				//list.style.left = -now * document.documentElement.clientWidth + 'px';
				//translateX = -now * document.documentElement.clientWidth;
				//list.style.transform = 'translateX('+ translateX +'px)';
				tranformCss(list,'translateX',-now * document.documentElement.clientWidth);
				
				//同步小圆点
				for (var i = 0;i<spanNodes.length;i++) {
					spanNodes[i].className = '';
				}
				spanNodes[now%spanNodes.length].className = 'active';
				
				auto ();
				
			});
			
			
			//自动轮播
			auto ();
			//模拟now值
			//var now = 0;
			
			function auto (){
				
				clearTime=setInterval(function(){
					
					if(now == lis.length-1){
						list.style.transition = 'none';
						now = spanNodes.length-1;
						tranformCss(list,'translateX',-now * document.documentElement.clientWidth);
					}
			
					setTimeout(function(){
						//自动轮播
						now++;
						list.style.transition = '0.5s';
						tranformCss(list,'translateX',-now * document.documentElement.clientWidth);
	
						//同步小圆点
						for (var i = 0;i<spanNodes.length;i++) {
							spanNodes[i].className = '';
						}
						spanNodes[now%spanNodes.length].className = 'active';
					},20);
					
				},2000);
			
			
			
			}
			
			
			
		}
	</script>
</html>





/*  JS 代码   */
(function(w){
	w.css = function(node,name,value){
		
		if(!node.transform){
			node.transform = {};
		};
		
		if(arguments.length > 2){
			//写
			var result = '';
			//向对象里面添加值
			node.transform[name] = value;
							
			for (var item in node.transform) {
														
				switch (item){
					case 'rotate':
					case 'skew':
					case 'skewX':
					case 'skewY':
						result += item + '('+ node.transform[item] +'deg) ';
						break;
					case 'scale':
					case 'scaleX':
					case 'scaleY':
						result += item + '('+ node.transform[item] +') ';
						break;
					case 'translate':
					case 'translateX':
					case 'trnslateY':
						result += item + '('+ node.transform[item] +'px) ';
						break;
				}												
			}									
		
			node.style.transform = result;
			
		}else{
			//读
			if(typeof node.transform[name] == 'undefined'){
				//默认值
				if(name == 'scale' || name == 'scaleX' || name == 'scaleY'){
					value = 1;
				}else{
					value= 0;
				}
				
				
			}else{
				//处理正常值
				value = node.transform[name];
			}
			
			return value;
		};
	
	
	}
})(window);

