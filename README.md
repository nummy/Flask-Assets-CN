# Flask-Assets中文参考
Flask-Assets插件用于将webassets整合进Flask项目中。
### 安装
使用下面命令进行安装
```
$ easy_install Flask-Assets
```
或者使用pip进行安装：
```
$ pip install Flask-Assets
```
### 使用说明
首先，创建一个```Environments```实例，并使用它初始化```Flask```应用，然后将```Bundle```对象注册到```Assets```上。
```
from flask import Flask
from flask.ext.assets import Environment, Bundle

app = Flask(__name__)
assets = Environment(app)

js = Bundle('jquery.js', 'base.js', 'widgets.js',
            filters='jsmin', output='gen/packed.js')
assets.register('js_all', js)
```
创建```Bundle```对象可以传递任意数量的源文件作为参数，使用```output```参数指定输出文件路径，使用```filters```参数指定过滤器。所有文件的路径都是相对路径，以应用的静态文件路径问基准。
除此之外，还可以从外部文件中读取配置来创建```Bundle```对象，例如```YAML```文件。
跟其他```Flask```扩展一样,一个```Bundle```对象可以被多个Flask应用使用，只需要调用```Bundle```对象的```init_app```方法即可。
```
app = Flask(__name__)
assets = flask.ext.assets.Environment()
assets.init_app(app)
```
### 使用打包文件
定义好资源文件assets之后，就可以将上配置的源文件进行合并与压缩处理，最终在页面中使用如下方式引用打包好之后的静态js文件。
```
{% assets "js_all" %}
    <script type="text/javascript" src="{{ ASSET_URL }}"></script>
{% endassets %}
```
每当第一次渲染模板的时候```Flask-Assets```都会对配置好的静态文件进行合并压缩处理，如果静态文件发生改变，也会自动重新打包。如果在app的配置中设置```ASSETS_DEBUG=True```，则每个源文件都会单独输出，而不会合并成一个文件。
### Flask 蓝图
如果在项目中用到蓝图，则可以给源文件加上蓝图的前缀来引用蓝图中的静态文件。例如：
```
js = Bundle('app_level.js', 'blueprint/blueprint_level.js')
```
在上面的例子中，```Bundle```将打包以下两个文件：
```
{APP_ROOT}/static/app_level.js
{BLUEPRINT_ROOT}/static/blueprint_level.js.
```
如果你之前使用过Python的webassets库的话，就应该知道是用webassets的时候必须设置```directory```和```url```参数，但是在使用```Flask-Assets```的使用并不需要，因为```Flask-Assets```使用应用的静态文件路径作为代替。但是```Flask-Assets```中也可以自定义```diectory```与```url```，这种情况下```Flask-Assets```就不能在Flask的蓝图中使用了。
### 直接在模板中进行配置
也可以直接在模板文件中配置打包文件：
```
{% assets filters="jsmin", output="gen/packed.js",
          "common/jquery.js", "site/base.js", "site/widgets.js" %}
    <script type="text/javascript" src="{{ ASSET_URL }}"></script>
{% endassets %}
```
### 配置

webassets有许多配置选项，在```Flask-Assets```中既可以通过```Environment```实例对象进行设置，也可以通过```app```来进行设置。下面两条语句的效果是等价的。
```
assets_env.debug = True
app.config['ASSETS_DEBUG'] = True
```
更多的配置选项，请查看```webassets```的官方文档。
### 管理命令
如果你已经安装了````Flask-Script```,就可以使用```flaskext.assets.ManageAssets```命令：
```
from flask.ext.assets import ManageAssets
manager = Manager(app)
manager.add_command("assets", ManageAssets(assets_env))
```
上述的命令让开发者可以使用命令行命令来重新打包资源文件。
