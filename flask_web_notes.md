# 第一章
## 虚拟环境
虚拟环境是python解释器的一个私有副本，在这个环境中你可以安装私有包，而且不会影响系统中的全局python解释器。

虚拟环境可以避免安装的python版本和包与系统预装的发生冲突。为每个项目单独创建虚拟环境，可以保证应用只能访问所在虚拟环境中的包，从而保持全局解释器的干净整洁，使其只作为创建更多虚拟环境的源。

在python3中，虚拟环境由python标准库中的venv包原生支持。

如果是Ubuntu Linux系统预装的Python3，那么标准库中没有venv包。通过以下命令安装python3-venv包：
```
sudo apt-get install python3-venv
```

创建虚拟环境的命令如下：
```
python3 -m venv virtual-environment-name
```

若想使用虚拟环境，要先将其激活，Linux或macOS使用以下命令：
```
source venv/bin/activate
```

Windows使用以下命令：
```
venv\Scripts\activate
```

激活虚拟环境后，在命令提示符中输入python，将调用虚拟环境中的解释器，而不是系统全局解释器，虚拟环境中的工作结束后，在命令提示符中输入deactivate，还原当前终端会话的PATH环境变量，把命令提示符重置为最初的状态。

## 安装Flask
在虚拟环境中利用pip安装
```
(vnev)$ pip install flask
```

安装完成后在python环境中尝试导入Flask：
```
(venv)$ python
>>>import flask
>>>
```

如果没有错误提醒，说明安装成功。

# 第二章 
## 2.1 初始化
所有的Flask应用都必须创建一个应用实例。Web服务器使用一种名为Web服务器网关接口（WSGI， Web server gateway interface）的协议，把接收自客户端的所有请求都转交给这个对象处理。应用实例是Flask类的对象：
```
from flask import Flask
app = Flask(__name__)
```

Flask类的构造函数只有一个必须指定的参数，即应用主模块或包的名称。在大多数应用中，Python的__name__变量就是所需的值。（Flask用这个参数确定应用的位置，进而找到应用中其他文件的位置，如图像和模板等）

## 2.2 路由和视图函数
客户端（例如Web浏览器）把请求发送给Web服务器，Web服务器再把请求发送给Flask应用实例。应用实例需要知道对每个URL的请求要运行哪些代码，所以保存了一个URL到Python函数的映射关系。处理URL和函数之间关系的程序称为路由。

在Flask应用中定义路由使用应用实例提供的app.route装饰器：
```
@app.route('/')
def index():
    return '<h1>Hello World!</h1>'
```

使用装饰器注册视图函数是首选方法，但不是唯一，Flask还支持一种更传统的方式：
```
def index():
    return '<h1>Hello World!</h1>'

app.add_url_rule('/', 'index', index)
```

Flask也支持URL中包含可变内容，如下例：
```
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, {}!</h1>'.format(name)
```
路由URL中放在尖括号里的内容就是动态部分，任何能匹配静态部分的URL都会映射到这个路由上。调用视图函数时，Flask会将动态部分作为参数传入函数。在这个视图函数中，name参数用于生成个性化的欢迎消息。
路由中的动态部分默认使用字符串，不过也可以是其他类型。例如，路由/user/<int: id>只会匹配动态片段id为整数的URL，例如/user/123。Flask支持在路由中使用string、int、float、path类型。path是一种特殊的字符串，与string类型不同的是，它可以包含正斜线。

## 2.3 一个完整的应用
示例：
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello World!</h1>'
```

## 2.4 Web开发服务器
Flask自带Web开发服务器，通过flask run命令启动。这个命令在FLASK_APP环境变量指定的Python脚本中寻找应用实例。

在虚拟环境中启动上节应用命令如下：
```
(venv)$ export FLASK_APP=hello.py
(venv)$ flask run
```

Windows中：
```
(venv)$ set FLASK_APP=hello.py
(venv)$ flask run
```

通过python解释器运行方式：
无需设置FLASK_APP环境变量，运行应用的主脚本中添加：
```
if __name__ == '__main__':
    app.run()
```
命令行中
```
python hello.py
```

## 2.5 动态路由
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello World!</h1>'

@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, {}!</h1>'.format(name)
```

## 2.6 调试模式
在执行flask run 命令之前设定FLASK_DEBUG=1环境变量或者在主脚本代码中使用
```
app.run(debug=True)
```
开启调试模式后，Flask会监视项目中的所有源码文件，发现变动时自动重启服务器，让改动生效。

## 2.7 略

## 2.8 请求-响应循环
<font color=red>看不懂，先跳过以后再学习</font>

# 第三章 模板
模板是包含响应文本的文件，其中包含用占位变量表示的动态部分，其具体值只在请求的上下文中才能知道。使用真实值替换变量，再返回最终得到的响应字符串，这一过程称为渲染，Flask使用一个名为Jinja2的强大模板引擎。（注：与使用python自动生成word文档的模板引擎相同，语法也相同！！）

### 渲染模板
默认情况下，Flask在应用目录中的templates子目录里寻找模板。
```
from flask import Flask, render_template
# ...

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/user/<name>')
def user(name):
    return render_template('user.html', name=name)
```

Flask提供的render——template()函数把Jinja2模板引擎集成到了应用中，这个函数的第一个参数是模板文件的文件名，随后的参数都是键值对，表示模板中变量对应的具体值。
name = name是经常使用的关键字参数，左边的name表示参数名，就是模板中使用的占位符，右边的name是当前作用域中的变量，表示同名参数的值。

### 变量
在模板中使用的{{ name }}结构表示一个变量，这是一种特殊的占位符，告诉模板引擎这个位置的值从渲染模板时使用的数据中获取。

变量的值可以使用过滤器修改，过滤器添加在变量名之后，二者之间以竖线分隔。例如下述模板把name变量的值变成首字母大写的形式:
```
Hello, {{ name|capitalize}}
```

常见Jinja2过滤器：safe、capitalize、lower、upper、title、trim、striptags

### 控制结构
Jinja2中条件判断语句：
```
{% if user %}
    Hello, {{ user }}!
{% else %}
    Hello, Stranger!
{% endif %}
```

另一种是在模板中渲染一组元素。下例展示了如何使用for循环实现：
```
<ul>
    {% for comment in comments %}
        <li>{{ comment }}</li>
    {% endfor %}
</ul>
```

Jinja2还支持宏。类似于Python中的函数，如：
```
{% macro render_comment(comment) %}
    <li>{{ comment }}</li>
{% endmacro %}

<ul>
    {% for comment in comments %}
        {{ render_comment(comment) }}
    {% endfor %}
</ul>
```

为了重复使用宏，可以把宏保存在单独文件中，然后在需要使用的模板中导入：
```
{% import 'macro.html' as macros %}
<ul>
    {% for comment in comments %}
        {{ macros.render_comment(comment) }}
    {% endfor %}
</ul>
```

不仅是宏，所有Jinja2模板代码都可以写入单独文件，通过：
```
{% include 'common.html' %}
```
方式引用

模板继承，类似于Python中的类继承。首先，创建base.html的基模板：
```
<html>
<head>
    {% block head %}
    <title>{% block title %}{% endblock %} - My Application</title>
    {% endblock %}
</head>
<body>
    {% block body %}
    {% endblock %}
</body>
</html>
```
基模板的衍生模板：
```
{% extends "base.html" %}
{% block title %}Index{% endblock %}
{% block head %}
    {{ super() }}
    <style>
    </style>
{% endblock %}
{% block body %}
<h1>Hello, World!</h1>
{% endblock%}
```
<font color=red>extends指令声明这个模板衍生自base.html。在extends指令之后，基模板中的3个区块被重新定义，模板引擎会将其插入适当的位置。如果基模板和衍生模板中de同名区块中都有内容，衍生模板中的内容将显示出来。在衍生模板中可以调用super(),引用基模板中同名区块里的内容。</font>

## 使用Flask-Bootstrap集成Bootstrap
Bootstrap是Twitter开发的一个开源Web框架，提供了用户界面组件可用于创建整洁且具有吸引力的网页，而且兼容所有现代的桌面和移动平台Web浏览器。

<font color=red>Bootstrap是客户端框架，不直接涉及服务器。服务器只需要提供引用了Bootstrap层叠样式表（CSS）和JavaScript文件的HTML响应，并在HTML、CSS和JavaScript代码中实例化所需的用户界面元素。这些操作最理想的执行场所就是模板。</font>

Flask-Bootstrap扩展安装：
pip install flask-bootstrap

使用：
```
from flask_bootstrap import Bootstrap
# ...
bootstrap = Bootstrap(app)
```

初始化Flask-Bootstrap后，就可以在应用中使用一个包含所有Bootsrap文件和一般结构的基模板。利用Jinja2的模板继承机制来扩展这个基模板。
```
{% extends "bootstrap/base.html" %}
{% block title %}Flasky{% endblock %}
{% block navbar %}
<div class="navbar navbar-inverse" role="navigation">
<div class="container">
<div class="navbar-header">
<button type="button" class="navbar-toggle"
data-toggle="collapse" data-target=".navbar-collapse">
<span class="sr-only">Toggle navigation</span>
<span class="icon-bar"></span>
<span class="icon-bar"></span>
<span class="icon-bar"></span>
</button>
<a class="navbar-brand" href="/">Flasky</a>
</div>
<div class="navbar-collapse collapse">
<ul class="nav navbar-nav">
<li><a href="/">Home</a></li>
</ul>
</div>
</div>
</div>
{% endblock %}
{% block content %}
<div class="container">
<div class="page-header">
<h1>Hello, {{ name }}!</h1>
</div>
</div>
{% endblock %}
```

## 自定义错误页面（基于Bootstrap）
使用app.errorhandler装饰器为404（请求未知页面或路由错误）和500（应用有未处理的异常）这两个错误提供自定义的处理函数。
```
@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500)
def internal_server_error(e):
    return render_templater('500.html'), 500
```
与视图函数一样，错误处理函数也返回一个响应。此外，错误处理函数还要返回与错误对应的数字状态码，状态码可以直接通过第二个返回值指定。

错误处理函数中引用的模板也需要自己编写。利用Jinja2的模板继承机制编写这两个html。应用可以定义一个具有统一页面布局的基模板（类比ppt模板），其中包含导航栏，而页面内容则留给衍生模板定义。
base.html定义如下：
```
{% extends "bootstrap/base.html" %}
{% block title %}Flasky{% endblock %}
{% block navbar %}
<div class="navbar navbar-inverse" role="navigation">
<div class="container">
<div class="navbar-header">
<button type="button" class="navbar-toggle"
data-toggle="collapse" data-target=".navbar-collapse">
<span class="sr-only">Toggle navigation</span>
<span class="icon-bar"></span>
<span class="icon-bar"></span>
<span class="icon-bar"></span>
</button>
<a class="navbar-brand" href="/">Flasky</a>
</div>
<div class="navbar-collapse collapse">
<ul class="nav navbar-nav">
<li><a href="/">Home</a></li>
</ul>
</div>
</div>
</div>
{% endblock %}
{% block content %}
<div class="container">
{% block page_content %}{% endblock %}
</div>
{% endblock %}
```

衍生404.html页面定义如下：
```
{% extends "base.html" %}
{% block title %}Flasky - Page Not Found{% endblock %}
{% block page_content %}
<div class="page-header">
<h1>Not Found</h1>
</div>
{% endblock %}
```

## 静态文件
默认设置下，Flask在应用根目录中名为static的子目录中寻找静态文件。如果需要，可在static文件夹中使用子文件夹存放文件。服务器收到映射到static路由上的URL后，生成的响应包含文件系统中对应文件的内容。

下例为在应用的基模板中引入favicon.ico图标。
```
{% block head %}
{{ super() }}
<link rel="shortcut icon" href="{{ url_for('static', filename='favicon.ico') }}"
    type="image/x-icon">
<link rel="icon" href="{{ url_for('static', filename='favicon.ico') }}"
    type="image/x-icon">
{% endblock %}
```
这个图标的声明插入head 区块的末尾。注意，为了保留基模板中这个区块里的原始内容，
我们调用了super()。

## 使用Flask-Moment本地化日期和时间
有一个使用JavaScript 开发的优秀客户端开源库，名为Moment.js，它可以在浏览器中渲染
日期和时间。Flask-Moment 是一个Flask 扩展，能简化把Moment.js 集成到Jinja2 模板中
的过程。Flask-Moment 使用pip 安装：
```
pip install flask-moment
```
这个扩展的初始化方法与Flask-Bootstrap 类似:
```
from flask_moment import Moment
moment = Moment(app)
```

在基模板的scrits块引入这个库，同时保留基模块中定义的原始内容：
```
{% block scripts %}
{{ super() }}
{{ moment.include_moment() }}
{% endblock %}
```

为了处理时间戳，Flask-Moment 向模板开放了moment 对象。把变量
current_time 传入模板进行渲染:
```
from datetime import datetime

@app.route('/')
def index():
    return render_template('index.html', current_time=datetime.utcnow())
```
在index.html中使用Flask-Moment 渲染时间戳：
```
<p>The local date and time is {{ moment(current_time).format('LLL') }}.</p>
<p>That was {{ moment(current_time).fromNow(refresh=True) }}</p>
```
format('LLL') 函数根据客户端计算机中的时区和区域设置渲染日期和时间。参数决定了
渲染的方式，从'L' 到'LLLL' 分别对应不同的复杂度。format() 函数还可接受很多自定义
的格式说明符。
第二行中的fromNow() 渲染相对时间戳，而且会随着时间的推移自动刷新显示的时间。这
个时间戳最开始显示为“a few seconds ago”，但设定refresh=True 参数后，其内容会随着
时间的推移而更新。如果一直待在这个页面，几分钟后会看到显示的文本变成“a minute
ago”“2 minutes ago”，等等。


Flask-Moment 实现了Moment.js 的format()、fromNow()、fromTime()、calendar()、valueOf()
和unix() 等方法。请查阅Moment.js 的文档（http://momentjs.com/docs/#/displaying/）， 学习这个库提供的全部格式化选项。

# 第四章 Web表单

使用HTML 可以创建Web 表单，供用户填写信息。表单数据由Web 浏览器提交给服务
器，这一过程通常使用POST 请求。

Flask-WTF 扩展可以把处理Web 表单的过程变成一种愉悦的体验。这个扩展对独立的
WTForms 包进行了包装，方便集成到Flask 应用中。
Flask-WTF 及其依赖可使用pip 安装：
(venv) $ pip install flask-wtf

## 4.1 配置
<font color=red>与其他多数扩展不同，Flask-WTF 无须在应用层初始化，但是它要求应用配置一个密钥。
密钥是一个由随机字符构成的唯一字符串，通过加密或签名以不同的方式提升应用的安全
性。Flask 使用这个密钥保护用户会话，以防被篡改。每个应用的密钥应该不同，而且不
能让任何人知道。示例4-1 展示如何在Flask 应用中配置密钥。</font>
```
app = Flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'
```

app.config 字典可用于存储Flask、扩展和应用自身的配置变量。使用标准的字典句法就
能把配置添加到app.config 对象中。这个对象还提供了一些方法，可以从文件或环境中导
入配置。Flask-WTF 之所以要求应用配置一个密钥，是为了防止表单遭到跨站请求伪造（CSRF，cross-site request forgery）攻击。恶意网站把请求发送到被攻击者已登录的其他网站时，就会引发CSRF 攻击。Flask-WTF 为所有表单生成安全令牌，存储在用户会话中。令牌是一种加密签名，根据密钥生成。为了增强安全性，密钥不应该直接写入源码，而要保存在环境变量中。

## 4.2 表单类
使用Flask-WTF 时，在服务器端，每个Web 表单都由一个继承自FlaskForm 的类表示。这
个类定义表单中的一组字段，每个字段都用对象表示。字段对象可附属一个或多个验证函
数。验证函数用于验证用户提交的数据是否有效。
示例4-2 是一个简单的Web 表单，包含一个文本字段和一个提交按钮。
```
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired

class NameForm(FlaskForm):
    name = StringField('What is your name?', validators=[DataRequired()])
    submit = SubmitField('Submit')
```
在这个示例中，NameForm 表单中有一个名为name 的文本字段和一个名为submit 的提交按钮。StringField 类表示属性为type="text" 的HTML input 元素。SubmitField 类表示属性为type="submit" 的HTML input元素。字段构造函数的第一个参数是把表单渲染成HTML 时使用的标注（label）。
StringField 构造函数中的可选参数validators 指定一个由验证函数组成的列表，在接受
用户提交的数据之前验证数据。验证函数DataRequired() 确保提交的字段内容不为空。

## 4.3 把表单渲染成HTML
Flask-Bootstrap 扩展提供了一个高层级的
辅助函数，可以使用Bootstrap 预定义的表单样式渲染整个Flask-WTF 表单，而这些操作
只需一次调用即可完成。使用Flask-Bootstrap，上述表单可以用下面的方式渲染：
```
{% import "bootstrap/wtf.html" as wtf %}
{{ wtf.quick_form(form) }}
```

import 指令的使用方法和普通Python 代码一样，通过它可以导入模板元素，在多个模板
中使用。导入的bootstrap/wtf.html 文件中定义了一个使用Bootstrap 渲染Flask-WTF 表单
对象的辅助函数。wtf.quick_form() 函数的参数为Flask-WTF 表单对象，使用Bootstrap 的
默认样式渲染传入的表单。

templates/index.html：使用Flask-WTF 和Flask-Bootstrap 渲染表单
```
{% extends "base.html" %}
{% import "bootstrap/wtf.html" as wtf %}

{% block title %}Flasky{% endblock %}

{% block page_content %}
<div class="page-header">
    <h1>Hello, {% if name %}{{ name }}{% else %}Stranger{% endif %}!</h1>
</div>
{{ wtf.quick_form(form) }}
{% endblock %}
```
模板的内容区现在有两部分。第一部分是页头，显示欢迎消息。这里用到了一个模板条
件语句。Jinja2 的条件语句格式为{% if condition %}...{% else %}...{% endif %}。如果条件的计算结果为True，那么渲染if 和else 指令之间的内容。如果条件的计算结果
为False，则渲染else 和endif 指令之间的内容。在这个例子中，如果定义了name 变量，
则渲染Hello, {{ name }}!，否则渲染Hello, Stranger!。内容区的第二部分使用wtf.
quick_form() 函数渲染NameForm 对象。
