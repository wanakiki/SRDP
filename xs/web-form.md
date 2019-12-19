## 表单

将用户数据提交给服务器的方式，通常使用POST请求。对包含表单数据的POST请求来说，用户填写的信息通过request.form访问。生成表单的HTML代码和验证提交的表单数据单调且重复，Flask-WTF拓展对其进行了包装。

flask-WTF及其依赖可以使用pip进行安装。

### 配置

Flask-WTF无需在应用层初始化，但是他要求应用配置一个**密钥**。Flask使用这个密钥保护会话，以防篡改。每个应用的密钥应该不同且需要保密。配置方式：

```python
app = flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'
```

app.config 字典可用户存储Flask、拓展和应用自身的配置变量。这个对象还提供了一些方法，可以从文件或者环境中导入配置。

（未增强安全性，密钥不应直接写入源码，而要保存在环境变量中，在之后的学习中会有提到）

### 表单类

当使用Flask-WTF时，服务器端的每个表单都由一个继承自FlaskForm的类表示。这个类定义表单中的一组字段，每个字段都用对象表示。字段对象可附属一个或多个验证函数。验证函数用于验证用户提交的数据是否有效。定义表单类代码：

```python
from flask_wtf import FlaskForm
from wtfoems import StringField, SubmitField
from wtforms.validators import DataRequired

class NameForm(FlaskForm):
    name = StringField('What is your name?', validators=[DataRequired()])
    submit = SubmitField('Submit')
```

表单中的字段被定义为类变量（name、submit），而各个类变量的值是相应字段类型的对象。可选参数validators指定验证函数（内容不为空）。FlaskForm基类由Flask-WTF拓展定义，从flask_wtf中导入，然而，字段和验证函数却是直接从WTForms包中导入的。

文件上传相关：

```python
from flask_uploads import UploadSet, configure_uploads, IMAGES, patch_request_class
from flask_wtf.file import FileField, FileRequired, FileAllowed
```

### 将表单渲染为HTML

手动书写模板或者调用``wtf.quick_form()``函数

**文件上传相关需要自己额外安装flask_uploads模块。**

### 在视图函数中处理表单

视图函数index()，两个任务：渲染表单、接受用户在表单中填写的数据

```python
@app.route('/', methods=['GET', 'POST'])
def index():
    name = None
    form = NameForm()
    if form.validate_on_submit():
        name = form.name.data
        form.name.data = ''
    return render_template('index.html', form=form, name=name)
```

``render_template()``函数渲染模板，注意含有传参。

### 重定向和用户会话

在上面的代码中，用户输入名字提交表单，点击刷新按钮后会要求用户对再次提交表单进行确认，之所以出现这种情况，是因为刷新页面时浏览器会重新发送之前发送过的请求。如果前一个请求是包含表单数据的post请求，刷新页面后会再次提交表单。在大多数情况下这不是我们想执行的操作，因此浏览器要求确认。

因此，最好不要让web应用把POST请求作为浏览器发送的最后一个请求。这种需求的实现方式是，使用重定向作为POST请求的响应，而不是使用常规响应。

重定向是一种特殊的响应，相应内容包含的是URL，而不是HTML代码的字符串。浏览器收到这种响应时，会向重定向的URL发起GET请求，显示页面内容。这个技巧称为**Post/重定向/Get模式**。

但这种方式会引起另外一个问题。应用处理POST请求时，可以通过form.name.data获取用户输入的名字，然而请求结束后数据就不见了。因为这个POST请求使用了重定向，所以应用需要保存用户输入的名字，这样才能在之后使用用户的输入，构建真正的响应。

应用可以把数据存在**用户会话中**，时请求上下文中的变量，名为session。

重定向写法：

```python
@app.route('/', methods=['GET', 'POST'])
def index():
    form = NameForm()
    if form.validate_on_submit():
        session['name'] = form.name.data
        return redirect(url_for('index'))
    return render_template('index.html', form=form, name=session.get('name'))
```

当表单被提交后，会使视图函数调用redirect函数（由Flask提供，用于实现重定向，参数为重定向的URL）。通常情况下在这里需要使用``url_for``函数，获得路由内部名称对应的URL。在``render_template``中，name的传参需要从session中获取，使用get获取对应值避免查找不到时出现问题。

### 闪现消息

对用户的一种提示（例如登录表单出现错误重新渲染时需要对用户进行提醒），Flask内置函数``flash``可以实现。

```python
@app.route('/', methods=['GET', 'POST'])
def index():
    form = NameForm()
    if form.validate_on_submit():
        old_name = session.get('name')
        if old_name is not None and old_name != form.name.data:
            flash('Looks like you have changed your name!')
        session['name'] = form.name.data
        return redirect(url_for('index'))
    return render_template('index.html', form=form, name=session.get('name'))
```

需要注意，在调用flash之后，模板也要进行相应的修改对提示消息进行渲染。最好在基模板中渲染闪现消息，这样在所有的页面都能显示。使用``get_flashed_messages()``函数可以在模板内获取信息，通常情况下，因为可能存在多个闪现的消息，所以需要使用for循环处理所有的内容。

模板：

```html
{% block content %}
<div class="container">
    {% for message in get_flashed_messages() %}
    <div class="alert alert-warning">
        <button type="button" class="close" data-dismiss="alert">&times;</button>
        {{ message }}
    </div>
    {% endfor %}

    {% block page_content %}{% endblock %}
</div>
{% endblock %}
```

