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
跟其他```Flask```扩展一样,