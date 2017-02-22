﻿
================================
	搭建MVC结构(使用mvc设计模式搭建的框架)  
=================================
	基于MVC，解耦合 （高内聚，低耦合），优点：易维护、易扩展
	mvc结构的升级是只通过添加内容即可升级
	本MVC模式采用的是单一入口：
		如：http://localhost/lamp103/mvc/index.php?c=stu&a=add   //打开学生信息的添加界面
			其中m的值stu表示访问的是StuAction   a的值add表示是方法（动作）
			就是访问StuAction的add方法。
			
			index.php?c=order&a=del&id=100
            
            http://www.abc.com/index.php?c=user&a=add
            http://www.abc.com/user-add.html

	MVC: M（model模型）-V(view视图)--C(control控制)
	
	MVC是一种经典设计模式，其中M（model模型）、V(view视图)、C(control控制)，	通过请求访问了控制层C，C调度M模型获取我们所需的信息数据，然后再去加载V视图将结果整合后响应给我们。
	
	使用MVC设计模式搭建的项目结构叫框架
	
	MVC是一个框架模式，它强制性的使应用程序的输入、处理和输出分开。
	使用MVC应用程序被分成三个核心部件：模型、视图、控制器。它们各自处理自己的任务
	
 1. 创建目录：
		ORG 	第三方扩展类
		model 	M（模型）层目录(M)
		controller  C（控制）层目录(C)
		view	V(视图)  层目录（smarty的模板目录）
		public	公共资源目录
		libs	Smarty库（解压到这里即可）
		view_c	Smarty模板编译目录（可选）
		cache	Smarty静态缓存目录（可选）
		configs 配置文件目录
		
 2. 将自己写好的model类放到model目录下
		model/Model.class.php
 
 3. 在ORG目录下创建一个Tpl.php的smarty子类，用于初始化smarty（等同于以前的init.php）
	代码如下：
		//Smarty信息的初始化类
		class Tpl extends Smarty{
			public function __construct(){
				parent::__construct(); //构造父类
				//初始化Smarty对象中属性：
				$this->template_dir = "view";		//smarty模板目录
				$this->compile_dir = "view_c";		//smarty模板编译目录

				$this->config_dir = "configs";	//smarty配置文件目录

				$this->cache_dir = "cache";		//smarty模板静态缓存目录
				//$this->caching = true;			//是否开启静态缓存
				//$this->cache_lifetime = 3600;		//静态缓存时间（秒）

				//指定定界符
				$this->left_delimiter="{";	//左定界符
				$this->right_delimiter="}";	//右定界符
			}
		}

  4. 在controller目录下创建Controller类，继承Tpl类，文件名叫：Controller.php 
	  代码如下：
		//Controller的控制基类
		class Controller extends Tpl{
			public function __construct(){
				parent::__construct();
			}
			
			/**
			 *Controller初始化方法(在这个方法里根据参数a的值决定调用对应的方法)
			 *
			 */
			public function init(){
				//获取a参数的值
				$a  = isset($_GET["a"])?$_GET["a"]:"index"; //默认值为index
				//判断当前Controller是否存在此方法
				if(method_exists($this,$a)){
					//调用此方法
					$this->$a();
				}else{
					die("没有找到{$a}对应的方法");
				}	
			}
			
		}
		
	5. 在最外层，创建一个index.php的入口文件
		<?php
			//网站的主入口程序

			//自动加载类
			function __autoload($name){
				//$name = strtolower($name);//转成小写
				if(file_exists("./controller/{$name}.php")){
					require("./controller/{$name}.php");
				}elseif(file_exists("./model/{$name}.php")){
					require("./model/{$name}.php");
				}elseif(file_exists("./ORG/{$name}.php")){
					require("./ORG/{$name}.php");
				}elseif(file_exists("./libs/".ucfirst($name).".class.php")){
					require("./libs/".ucfirst($name).".class.php");
				}elseif(file_exists("./libs/sysplugins/{$name}.php")){
					require("./libs/sysplugins/{$name}.php");
				}else{
					die("错误：没有找到对应{$name}类!");
				}
			}
			//数据连接配置文件
			require("./configs/config.php");

			//获取参数m的值，并创建对应的Controller对象
			$mod = isset($_GET['c'])?$_GET['c']:"index";
			//拼装Controller类名
			$classname = ucfirst($mod)."Controller";
			//创建对应的Controller对象
			$controller = new $classname();

			//执行Controller的初始化（Controller入口）
			$controller->init();
			
	6. 在configs的目录下创建一个config.php的配置文件
	
	7. 测试：
-------------------------------------------------------------
		1. 在Controller目录下创建一个IndexController.class.php文件
				/**
				 * 网站入口的主Controller类
				 */
				class IndexController extends Controller{
					//默认入口方法
					public function index(){
						$this->assign("title","MVC的测试界面");
						$this->display("index.html");
					}
				}
		2. 在view目录下创建index.html模板页面	
			<html>
				<head>
					<title>{$title}</title>
				</head>
				<body>
					<h2>{$title}</h2>
				
				</body> 	
			</html>
			
			
			
			
	
class A{

  public function init(){
	$this->add();
  }
}

class B extends A{
	public function add(){
		echo "测试！";
	}
}

$b = new B();
$b->init();




http://localhost/lamp128_C/lesson24_mvc/demo1/index.php?c=stu&a=index
http://www.***.com/index.php/stu/index
http://
		www.***.com/stu/index
http://www.***.com/123-1.html		