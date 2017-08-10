﻿
这一小节重点介绍两个利器phantomjs和selenium.通过利用这些工具可以破解本地JS环境的反爬措施.

本地JS环境是个杀器，可以通杀没有本地环境的任意爬虫.其原理是因为浏览器自带有引擎，引擎中包含有js运行环境.所有js脚本都可以在浏览器上直接运行，这一切都是发生在本地浏览器环境内的，没有上述的与后台交互的行为，所有我们不能通过分析抓包得到的报文来绕过这类反爬措施。
所以，这时候我们就得用模拟浏览器内核来绕过这个限制.现在业内一般有两种方式.无图形化的phantomjs和有图形化的selenium.分别属于无头浏览器与webdriver两个阵营.下面我们就分为介绍一下:

 - 无头浏览器，具体来说是一个除了图形化界面外其余都与普通浏览器毫无区别的工具。运用无头浏览器，我们可以实现在爬虫运行过程中让js脚本运行在headlessa浏览器中，从而模拟出与一般浏览器一样的页面。然后我们就能如同一般情况下对该网页进行解析来提取数据.我们用目前比较热门的[phantomjs](http://phantomjs.org/)浏览器来举例，phantomjs是一个采用的是Webkit内核与目前的Safari，Chrome等浏览器兼容性十分好的无头浏览器。可以用来模拟所有浏览器操作，被广泛运用于自动化测试和web爬虫中.
 - webdriver，此类一般用作自动化测试较多，用于爬虫的一般多用作测试环境.因为无头浏览器没有GUI图形界面，爬取数据还好说，看有无数据返回亦或者数据返回的对错，但如果是要通过点击或者其他行为才能获取到数据的页面，那么没有GUI界面的就无法即使的直到运行的对错，这时候就需要在类似[selenium](http://www.seleniumhq.org/)这种webdriver上进行自动化操作，来模拟无头浏览器上的操作。在爬虫这方面，[selenium](http://www.seleniumhq.org/)等于一个有界面的无头浏览器.

不过如果使用了这种方式来绕过的话，运行性能上会受到严重影响.
因为首先模拟浏览器就很费性能，然后还有本地运算JS更是消耗资源的大户.所以比起其他方法来说的话，这方面毫无疑问的会慢很多.如果存在其他方法的话，请尽量不要用此方法.