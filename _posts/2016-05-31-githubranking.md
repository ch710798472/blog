---
layout: post
title: Github Analytics项目解析
category: java
modified: 2016-05-02
tags: [django,github]
comments: true
pinned: true
excerpt: "1. 准备工作
　　1）百度腾讯云官网，注册，认证（不是学生的跳过学生认证）。
　　2）安装apache,git,pip,django等工具
　　3) 测试安装结果...."
---
####1.环境搭建
#####1.1 系统-Ubuntu
　　首先我使用的是Ubuntu14.04版本,因为这个版本相对稳定，并且相对于12.04这个版本性能更好一些和支持的功能更多一些，不太建议用最新的版本。如果你对于Ubuntu系统不是很了解，我建议你去看一下这本书<<鸟的Linux私房菜>>，熟悉下Linux系统的命令和系统本身的一些特点，这些都是软实力有助于你更加高效的编写代码和查找错误。如果是第一次接触这个系统的同学，我建议一开始使用虚拟机来搭建系统，双系统很容易由于操作失误导致电脑崩溃挂掉，虚拟机的安装和保存副本都很容易想怎么折腾就怎么折腾。这里你可以使用VMware或者virtual box，其中VMware性能和功能支持性比较好，VBox小巧玲珑够用了，看你们自己选择吧。如果这方面也不太熟悉，在网上百度两篇博文看看就行了，没有必须去系统的学习，用到的功能都是比较单一的，碰到解决不了的问题再去想办法解决。
　　再来就是如果你想对Linux系统本身有一个更好的了解的话，建议去看看Linux源码，当当上都有卖的<<Linux内核完全注释>>，赵炯大师所著。一般看0.11版本的就可以了，因为比较小巧（其实是很厚的一本源码解析的书也不少）。
　　`tips: `这里有一些你可能感兴趣的资料可以看看。[《命令行的个性化》](http://ch710798472.github.io/blog/ubuntu/2015/11/09/ubuntu-terminal.html)[《让你的ubuntu美起来》](http://ch710798472.github.io/blog/ubuntu/2015/12/01/ubuntu-beautiful.html)
#####1.2 开源的世界-git
　　如果你还不知道Google的github的话，那么你已经落伍了，或者你还在用SVN其他的项目管理工具，那么你该学学git这个命令的强大之处了。
　　git命令可以在Ubuntu下这么安装
```
sudo apt-get install git
```
　　安装完之后你就可开启你的git之旅了。首先你需要去github的[官网](www.github.com)去注册账号，注册完毕之后会有新手指引，但是你需要做的就是新建一个仓库然后在setting里的SSH KEY里面添加给你的机器上去，具体怎么添加[看这里](http://www.xuanfengge.com/using-ssh-key-link-github-photo-tour.html)。然后在命令行中配置你的github账号邮箱等你就可以把你的机器和你的github账号关联起来了而不用每次输入用户名密码了，很方便。完整的过程在这里[点我](http://www.512876.com/2013a/0605/1.html)
　　如果你仅仅认为github只是一个协同开发的开源项目管理工具你就错了，你还可以搭建你自己的博客（当然不是万能的只是静态博客，想要完美的个性博客还是买服务器自己搭建博客吧）。下面给出搭建博客的一些介绍：[《如何搭建自己的github博客》](http://ch710798472.github.io/blog/personal/2015/11/03/install-blog.html)
　　[《通过GitHub Pages建立个人站点（详细步骤）》](http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html)
　　[《如何搭建一个独立博客——简明Github Pages与Hexo教程》](http://www.jianshu.com/p/05289a4bc8b2)
　　[《搭建一个免费的，无限流量的Blog—-github Pages和Jekyll入门》](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)

####2.准备工作
#####2.1 python语言
　　由于我们的项目是基于Django的一个项目，也就是说后台的代码全部是用python来写的所以在这里你需要学习下python的基本语法就够了，不需要你们去研究高级的特性，其实每一门语言入门简单，精通不易。还好我们这个项目只需要入门就够了，学习python语言的途径有很多，网上有很多的网站、教程以及书籍，这里我就推荐一本书吧--《python基础教程》。
　　看完书籍或者学完教程，实践才能检验学习的成果，我建议还是写两个练手的python小程序来熟悉来巩固基础的知识。你可以在github上搜一下，也可以上知乎问问有经验的大侠，[比如知乎上这里的答案几乎可以满足你的要求。](https://www.zhihu.com/question/29372574)
#####2.2 Django+bootstrap+d3.js
　　python语言的基础打好了，我们还需要了解python的一些web框架，这里我们采用的是django，这个框架结构简单上手快(貌似web框架都上手快)。学习django很简单，百度自强学堂，个人觉得他们的教程做的还不错，一定会让你受益匪浅。但是如果你没有接触过一些前端技术，比如html、js、jQuery等，我建议还是去补一下这方面的知识，很多人说我不做前端所以不需要知道，但是我想告诉你的是，你即使是写算法或者写后台或者做运维等等，在你的计算机学习生涯你还是一定会碰到需要用web来解决的时候，web端不管是搭建一个项目的demo还是作为一个团队的门户亦或者作为一款产品都是一个不错的选择，你在做项目的时候还在用java swing写客户端页面？还在用MFC？那不如来个网址让我们看看你实际做了什么实在，没有人会去安装你的产品，我们需要的仅仅是看看你的程序能否打动我，这个时候掏出iPad或者手机，访问我们的网站一目了然。（好像扯远了，不过前端的一些技术学起来蛮快的，所以何不花点时间增加你的软实力呢！）
　　bootstrap这个前端框架很好用，基本上不需要很多修改就能拿到自己的网站使用，举个例子你去看百度云的首页，是不是和我们的项目首页是不是有些相似，那么还在犹豫什么，赶紧去学，百度bootstrap会有中文的文档照着看看就差不多了，了解之后直接看项目的源码就行了，没必要深究下去。毕竟不求甚解有时候可以让我们继续走下去。
　　D3.js是一个js写的数据可视化的工具，我们可以利用别人封装好的API来可视化我们的项目，而且很漂亮，交互性也很好。学习它的话有书籍一本《d3.js数据可视化实战手册》，[网站一个](http://www.ourd3js.com/wordpress/?p=1696)。
#####2.3 python数据分析基础
　　由于项目是一个大数据分析的项目，所以需要你知道一些数据分析的知识。虽然本项目中用到的数据分析知识不多，但是我在项目里面有一个子项目，一个被自我否定的项目，里面写了很多的数据分析的内容和算法。所以大胆的去学，这里推荐几本书《利用Python进行数据分析》《python数据分析基础教程：numpy学习指南》《机器学习实战》《数据挖掘：概念与技术（原书第3版）》。这里推荐从第一本开始看，先了解下数据分析然后熟悉下python数据分析工具包的使用，然后在实战，最后来看看理论基础，可能相比于一开始就看数据挖掘的枯燥无味的理论要来的实在。

####3.源码解析
#####3.1 项目准备
　　项目源码地址看[这里](https://github.com/ch710798472/GithubRecommended)，readme我已经写了怎么上手这个项目以及开发环境的搭建，但是由于篇幅太短，在这里我还是在表述下安装过程。
　　git clone我的项目之后，你需要安装一下这些安装包：
```
sudo apt-get install pip
sudo pip install numpy
sudo pip install scipy
sudo apt-get install python-networkx
sudo pip install pygithub
sudo pip install pandas
sudo pip install d3py
```
　　可能有的安装命令失效了，那么pip的你可以用apt-get试试比如pip install numpy没有用你可以apt-get install python-numpy代替。安装完毕，可能很多的同学都说怎么写代码，改代码，这里推荐pycharm这个开发工具，还是比较好用的网上有很多的下载链接，只需要找一个注册码就可以永久使用付费版了。
#####3.2 源码剖析
　　我们来看一下项目的目录结构：![](http://i.imgur.com/j5zg5qx.png)发现和django的结构差不多.idea先忽略，RecGithub里面存放的就是后台的代码，RecGithub/templates里面是页面模板，Web里面是django项目的一些配置，static里面是一些前端的JS等一些静态数据。
#####3.2.1 /Web目录
　　/urls.py文件中，我们可以看到整个项目的URL设置，比如form是搜索时候用的，admin是后台入口，其他的都是什么功能可以去试试就知道了，这很简单。
```
urlpatterns = [
    url(r'^$',views.index,name='home'),
    url(r'^form/$',views.form,name='form'),
    url(r'^repo/$',views.repo,name='repo'),
    url(r'^connect/$',views.connect,name='connect'),
    url(r'^add/(\d+)/(\d+)/$', views.add, name='add'),
    url(r'^search/$', views.search,name='search'),
    url(r'^nonconnect/$', views.nonconnect,name='nonconnect'),
    url(r'^admin/', admin.site.urls),
    url(r'^home/$',views.index,name='home'),

]
```
　　settings.py中要注意的是静态文件的设置，我们可以看到对不同的系统中的文件的路径的一个特殊处理。其中debug的设置，在开发的时候设置为true可以更好的帮助我们来调试代码。
```
STATIC_URL = '/static/'
STATICFILES_DIRS = (
    os.path.join(os.path.dirname(__file__), '../static/').replace('\\','/'),
)
```
#####3.2.2 /RecGithub目录
　　views.py文件定义了文件的视图控制结构包括页面的跳转控制等。
```
def index(request): #这里定义了首页的内容
    return render(request, 'index.html')

def form(request): #这里是搜索查找的时候所用到的表格与models.py中定义的表单相对应
    if request.method == 'POST':   # 当提交表单时

        form = SearchForm(request.POST)  # form 包含提交的数据

        if form.is_valid():  # 如果提交的数据合法
            location = form.cleaned_data['location']
            language = form.cleaned_data['language']

            Dict = {'filename':location+language}
            if GetSearchInfo(location,language):
                return render(request,'search_result.html',{'Dict':json.dumps(Dict)})
            else:
                return HttpResponse(str("查找结果不存在，请重新输入！"))

    else:  # 当正常访问时
        form = SearchForm()
    return render(request, 'search.html', {'form': form})

def repo(request): #这是定义了搜索仓库的业务逻辑
    if request.method == 'POST':  # 当提交表单时

        form = SearchRepoForm(request.POST) # form 包含提交的数据

        if form.is_valid():  # 如果提交的数据合法
            stars = form.cleaned_data['stars']
            language = form.cleaned_data['language']

            Dict = {'filename':language+stars}
            if SearchRepo(stars,language):
                return render(request,'repo_result.html',{'Dict':json.dumps(Dict)})
            else:
                return HttpResponse(str("查找结果不存在，请重新输入！"))

    else:  # 当正常访问时
        form = SearchRepoForm()
    return render(request, 'repo.html', {'form': form})

def connect(request): #这里定义了图谱关系的业务逻辑
    if request.method == 'POST':  # 当提交表单时

        form = ConnectForm(request.POST)   # form 包含提交的数据

        if form.is_valid():  # 如果提交的数据合法
            user = form.cleaned_data['user']
            repo = form.cleaned_data['repo']

            Dict = {'filename':user+repo}
            if SocialConnect(user,repo):
                return render(request,'connect_result.html',{'Dict':json.dumps(Dict)})
            else:
                return HttpResponse(str("查找结果不存在，请重新输入！"))

    else:  # 当正常访问时
        form = ConnectForm()
    return render(request, 'connect.html', {'form': form})

def search(request): #搜索逻辑处理
    searchKey = request.GET['searchKey']
    Dict = {'filename': searchKey.strip()}
    if searchKey.strip()=='':
         return HttpResponse(str("请输入查找关键字！"))
    else:
        if(SearchConnect(searchKey.strip())):
            return render(request, 'search_key_result.html',{'Dict':json.dumps(Dict)})
        else:
            return HttpResponse(str('请重新查找！'))

def nonconnect(request): #非实时搜索处理逻辑
    if request.method == 'POST':  # 当提交表单时

        form = ConnectForm(request.POST)  # form 包含提交的数据

        if form.is_valid():  # 如果提交的数据合法
            user = form.cleaned_data['user']
            repo = form.cleaned_data['repo']

            Dict = {'filename':user+repo}
            if nonSocialConnect(user,repo):
                return render(request,'connect_result.html',{'Dict':json.dumps(Dict)})
            else:
                return HttpResponse(str("查找结果不存在，请重新输入！"))

    else:  # 当正常访问时
        form = ConnectForm()
    return render(request, 'connect.html', {'form': form})
```
　　models.py中可以看到我们定义的自定义的django表单的类型：
```
#my search form
class SearchForm(forms.Form):
        location = forms.CharField(max_length=20 ,label='所在地区')
        # mail = forms.EmailField(label='电子邮件')
        # topic = forms.ChoiceField(choices=TOPIC_CHOICES,label='选择评分')
        language = forms.CharField(max_length=100 ,label='编程语言')
        def __unicode__(self):
            return self.name
```

　　/templates目录下是页面的html文件，采用的是django模板来设计的页面，把页面分成头、内容、尾三个部分。比如在base.html文件中，定义了模板：
```
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=0.618">
    <title>{% block title %}Github Analytics{% endblock %}</title>
</head>
<body>
 
{% include 'nav.html' %}
{% include 'form.html' %}
 
{% block content %}
<div>loading</div>
{% endblock %}
 
{#{% include 'bottom.html' %}#}
{# #}
{#{% include 'tongji.html' %}#}
 
</body>
</html>
```
　　然后在connect.html中继承模板就可以了：
```
{% extends 'base.html' %}

{% block title %}关系图谱{% endblock %}

{% block content %}
{#{% include 'ad.html' %}#}
{#{% url 'add' 4 5 %}#}
{#    <a href="{{ request.path }}?{{ request.GET.urlencode }}&delete=1">当前网址加参数 delete</a>#}
{#    {% for i in List %}#}
{#        {{ i }} {% if not forloop.last %},{% endif %}#}
{#    {% endfor %}#}
    <form id="formId" class="container" method='post'>
        {% csrf_token %}
{#        {{ form }}#}
        {% for field in form %}
            <div class="field">
                {{ field.label_tag }}{{ field }}
                {{ field.errors }}
            </div>
        {% endfor %}
        <input type="submit" class="buttoncss" value="搜索">
        <input type="button" class="buttoncss" name="Submit" value="返回" onclick="javascript:history.back(1)" />
    <p style="margin-top:50px; color:#F00">提示：我们的系统采用缓存加速模式，用的用户越多查询速度越快，如果您是第一个查询此类信息，请耐心等待。</p>
    </form>
{% endblock %}
```
　　这样的页面可维护性大大提高，而且有利于流畅性的提升以及代码的阅读。
  chgithub.py文件是整个项目的核心，里面有github人物关系图谱的数据获取，关系的建立，以及各个排名的算法。
```
def readcfg():
    '''
    read config.cfg file
    :return:github username,password
    '''
    config=ConfigParser.ConfigParser()
    with open('./RecGithub/config.cfg','r') as cfgfile:
        config.readfp(cfgfile)
    user=config.get('info','user')
    passwd=config.get('info','passwd')
    return user,passwd

def GetSearchInfo(location,language):
    '''
    search infomation
    :param location:
    :param language:
    :return:
    '''
    #search 100 users from location,language

    filename = "./static/bootstrap/data/" +location +language+ ".json"
    if os.path.exists(filename):
        return 1
    else:
        d = {"items":[]}
        url = 'https://api.github.com/search/users?sort=followers&q=location:%s+language:%s&per_page=30&page=' % (location,language)
        rank_count=1

        #获取排名
        print 'chgithub.GetSearchInfo->get start user'
        for i in range(rank_count):
            # print url + str(i+1)
            newUrl = url + str(i+1)
            r = requests.get(newUrl)
            temp = r.json()
            d['items'].extend(temp['items'])

        print 'chgithub.GetSearchInfo->finish rank user'

        #获取用户详细信息
        username,password=readcfg()

        client = Github(login_or_token=username,password=password, per_page=100)
        t = {}
        notdone=[]
        j=0
        for i in d['items']:
            try:
                user = client.get_user(i['login'])
                t[user._rawData['login']] = user._rawData
            except Exception,e:
                print "chgithub.GetSearchInfo->time out"
                print Exception,e
                notdone.append(i['login'])
            if (j%10)==0:
                print j
            j = j + 1

        print 'chgithub.GetSearchInfo->finish user info'

        #重新排序

        d1 = []#最后的结果存储
        for i in d['items']:
            for k in t:
                try:
                    if i['login'] == k:
#                       print k
                        d1.append(t[k])
#                       apendx.append(k)
                except Exception,e:
                    pass

        print 'chgithub.GetSearchInfo->DONE'

        if len(d1):
            json.dump(d1, open(filename, 'w'))
            return 1
        else:
            return 0

def SearchRepo(stars,language):
    filename = "./static/bootstrap/data/" +language +stars + ".json"
    if os.path.exists(filename):
        return 1
    else:
        d = {"items":[]}
        url = 'https://api.github.com/search/repositories?q=language:%s&stars:>%s&sort=stars&order=desc&per_page=100&page=' % (language,stars)
        rank_count=2

        #获取
        print 'chgithub.SearchRepo->get start repo'
        for i in range(rank_count):
            # print url + str(i+1)
            newUrl = url + str(i+1)
            r = requests.get(newUrl)
            temp = r.json()
            d['items'].extend(temp['items'])

        print 'chgithub.SearchRepo->finish rank repo'

        if len(d):
            json.dump(d, open(filename, 'w'))
            print 'chgithub.GetSearchInfo->DONE'
            return 1
        else:
            print 'chgithub.GetSearchInfo->DONE'
            return 0

def SocialConnect(USER,REPO):

    filename = "./static/bootstrap/data/" +USER +REPO+ ".json"
    if os.path.exists(filename):
        return 1
    else:

        username,password=readcfg()

        client = Github(login_or_token=username,password=password, per_page=100)

        user = client.get_user(USER)
        repo = user.get_repo(REPO)

        print 'chgithub.SocialConnect->get start'
        stargazers = [ s for s in repo.get_stargazers() ] #获得关注者，通常这个人数比较多
        contributors = [ s for s in repo.get_contributors() ] #获得贡献者

        g = nx.DiGraph()
        g.add_node(repo.name + '(r)', type='repo', lang=repo.language, owner=user.login)

        for sg in stargazers:
            g.add_node(sg.login + '(u)', type='user')
            g.add_edge(sg.login + '(u)', repo.name + '(r)', type='gazes')
        print 'chgithub.SocialConnect->finish add stargazers'

        for sg in contributors:
            g.add_node(sg.login + '(u)', type='user')
            g.add_edge(sg.login + '(u)', repo.name + '(r)', type='conbs')
        print 'chgithub.SocialConnect->finish add contributors'

        d = json_graph.node_link_data(g)
        json.dump(d, open(filename, 'w'))
        print 'chgithub.SocialConnect->DONE'
        return 1

def SearchConnect(searchKey):

    filename = "./static/bootstrap/data/" +searchKey + ".json"
    if os.path.exists(filename):
        return 1
    else:
        username,password=readcfg()

        client = Github(login_or_token=username,password=password, per_page=100)

        user = client.get_user(searchKey)
        repos = [s for s in user.get_repos()]

        print 'chgithub.SearchConnect->get start'

        g = nx.DiGraph()
        g.add_node(searchKey + '(u)', type='user')

        try:
            for r in repos:
                stargazers = [ s for s in r.get_stargazers() ]
                if(len(stargazers)):
                    g.add_edge(searchKey + '(u)',r.name + '(r)', type='have')

                for sg in stargazers:
                    g.add_node(sg.login + '(u)', type='user')
                    g.add_edge(sg.login + '(u)', r.name + '(r)', type='gazes')

                contributors = [ s for s in r.get_contributors() ]
                for sg in contributors:
                    g.add_node(sg.login + '(u)', type='user')
                    g.add_edge(sg.login + '(u)', r.name + '(r)', type='conbs')
        except Exception,e:
            print "chgithub.SearchConnect->time out"

        d = json_graph.node_link_data(g)
        json.dump(d, open(filename, 'w'))
        print 'chgithub.SearchConnect->DONE'
        return 1

def nonSocialConnect(USER,REPO):

    filename = "./static/bootstrap/data/" +USER +REPO+ ".json"
    if os.path.exists(filename):
        return 1
    else:

        username,password=readcfg()

        client = Github(login_or_token=username,password=password, per_page=100)

        user = client.get_user(USER)
        repo = user.get_repo(REPO)

        print 'chgithub.SocialConnect->get start'
        stargazers = [ s for s in repo.get_stargazers() ] #获得关注者，通常这个人数比较多
        contributors = [ s for s in repo.get_contributors() ] #获得贡献者

        g = nx.DiGraph()
        g.add_node(repo.name + '(r)', type='repo', lang=repo.language, owner=user.login)

        for sg in stargazers:
            g.add_node(sg.login + '(u)', type='user')
            g.add_edge(sg.login + '(u)', repo.name + '(r)', type='gazes')
        print 'chgithub.SocialConnect->finish add stargazers'

        for sg in contributors:
            g.add_node(sg.login + '(u)', type='user')
            g.add_edge(sg.login + '(u)', repo.name + '(r)', type='conbs')
        print 'chgithub.SocialConnect->finish add contributors'

        d = json_graph.node_link_data(g)
        json.dump(d, open(filename, 'w'))
        print 'chgithub.SocialConnect->DONE'
        return 1
```

####4.

####5.

####6.