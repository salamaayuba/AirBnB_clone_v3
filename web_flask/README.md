 8 years, 10 months ago
Modified 1 year, 2 months ago
Viewed 80k times

223


I have read the official Flask documentation on Blueprints and even one or two blog posts on using them.

I've even used them in my web app, but I don't completely understand what they are or how they fit into my app as a whole. How is it similar to an instance of my app but not quite? The documentation is comprehensive but I seek a layman explanation or an enlightening analogy to spark it for me. I was sufficiently perplexed when a colleague asked me to explain a Flask blueprint to them that I elected to ask here.

pythonflaskwsgi
Share
Follow
edited Jun 26, 2014 at 5:36
asked Jun 26, 2014 at 0:50
JoshieSimmons's user avatar
JoshieSimmons
2,71122 gold badges1313 silver badges2323 bronze badges
Add a comment
5 Answers
Sorted by:

Highest score (default)

328


A blueprint is a template for generating a "section" of a web application. You can think of it as a mold:

A medallion mold with a gold medallion freshly removed from it

You can take the blueprint and apply it to your application in several places. Each time you apply it the blueprint will create a new version of its structure in the plaster of your application.

# An example
from flask import Blueprint

tree_mold = Blueprint("mold", __name__)

@tree_mold.route("/leaves")
def leaves():
    return "This tree has leaves"

@tree_mold.route("/roots")
def roots():
    return "And roots as well"

@tree_mold.route("/rings")
@tree_mold.route("/rings/<int:year>")
def rings(year=None):
    return "Looking at the rings for {year}".format(year=year)
This is a simple mold for working with trees - it says that any application that deals with trees should provide access to its leaves, its roots, and its rings (by year). By itself, it is a hollow shell - it cannot route, it cannot respond, until it is impressed upon an application:

from tree_workshop import tree_mold

app.register_blueprint(tree_mold, url_prefix="/oak")
app.register_blueprint(tree_mold, url_prefix="/fir")
app.register_blueprint(tree_mold, url_prefix="/ash")
Once it is created it may be "impressed" on the application by using the register_blueprint function - this "impresses" the mold of the blueprint on the application at the locations specified by url_prefix.

Share
Follow
edited Feb 27, 2019 at 10:27
pfabri's user avatar
pfabri
87311 gold badge99 silver badges2525 bronze badges
answered Jun 26, 2014 at 1:11
Sean Vieira's user avatar
Sean Vieira
154k3232 gold badges311311 silver badges291291 bronze badges
12
Here is a nice article for application structure using blueprints. exploreflask.com/en/latest/blueprints.html – 
Devasish
 Jul 27, 2017 at 5:25
9
I am still confused. Does that mean oak/leaves and fir\leaves will point tot the same code? Also, what is the purpose of the string mold in Blueprint("mold", __name__) – 
Codevalley
 Aug 7, 2017 at 6:48 
11
Yes, both oak/leaves and fir/leaves will be handled by def leaves. "mold" is the name of the blueprint and is used for disambiguation when referring to routes (e. g. someBlueprint.routeName vs. anotherBlueprint.routeName). – 
Sean Vieira
 Aug 8, 2017 at 6:19
2
How can I access the url_prefix in the function? Say, to check if it is oak or fir? tree_mold.url_prefix gives me a None – 
Arjun
 Feb 12, 2018 at 17:49
3
@Mellkor - simply use url_for('.relative_route_name') (note the leading dot). For example, url_for('.roots') will provide a correctly scoped URL at each of the mounted points automatically. – 
Sean Vieira
 Feb 14, 2018 at 4:29
Show 6 more comments

11


As pointed out in a comment by @Devasish, this article provides a good answer:

http://exploreflask.com/en/latest/blueprints.html

Quoting from the article:

An example of this would be Facebook. If Facebook used Flask, it might have blueprints for the static pages (i.e. signed-out home, register, about, etc.), the dashboard (i.e. the news feed), profiles (/robert/about and /robert/photos), settings (/settings/security and /settings/privacy) and many more. These components all share a general layout and styles, but each has its own layout as well

This is a very good interpretation, especially the part "if Facebook used Flask". It gives us a concrete situation to visualize how Blueprint actually works.

Share
Follow
edited Jun 4, 2020 at 8:05
answered Oct 12, 2019 at 15:51
Tran Cuong's user avatar
Tran Cuong
50755 silver badges1010 bronze badges
Add a comment

8


For bigger projects, all your code shouldn't be in the same file. Instead you can segment or split bigger codes into separate files, mostly based on functionality. Like bricks forming a wall.

A simple Flask app
app = Flask(__name__)
A blueprinted Flask app
import from_any_module.part_1
import from_other_module.part_2

app = Flask(__name__)

app.register_blueprint(part_1)
app.register_blueprint(part_2)
A blueprint in the above app
from flask import Blueprint
part_1 = Blueprint(part_1)

@part_1.route('/url')
def function()
    return view
Share
Follow
edited Jan 24, 2022 at 2:49
answered May 15, 2020 at 5:11
Henshal B's user avatar
Henshal B
1,37099 silver badges1111 bronze badges
Add a comment

7


I too just stumbled up this myself and was confused after reading a few of the documentation sources. At first I thought it was like C#/Java OOP Interface Implementation style where you define some stuff but dont have to worry about it implementation details til later. However, I stumbled up this page which puts it in very very laymens (and quite hilarious present-day events) terms. https://hackersandslackers.com/flask-blueprints/

Essentially one benefit that is mentioned in the link and provides me a clear idea of it's real world usage is that I can effectively logically organize/divide the app into several parts that only need to be concerned with it's own affairs. So it provides some designed encapsulation.

Edit: I'm currently using it to segment out my webapps code. It was good decision too because I found the lead designer wants to make the frontend in Vue.js. Which I havent used yet but looking at it's project files would look far more messy and probably provide many naming collision prone instances due to files with similar names

Share
Follow
edited Feb 9, 2022 at 0:56
answered Jan 17, 2020 at 10:21
LFMekz's user avatar
LFMekz
57388 silver badges1010 bronze badges
Add a comment

2


A Flask blueprint helps you to create reusable instances of your application. It does so by organizing your project in modules. Those modules are then registered the main application. They help in creating an application factory.
