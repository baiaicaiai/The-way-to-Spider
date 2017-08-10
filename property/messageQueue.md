本小节开始介绍之前多次提到过的消息队列。再讲之前，问大家各问题.你们遇到过有些时候，爬虫有很大一部分时间抓取解析模块是空闲的情况么？我猜应该大多数都遇到过.产生这个现象的原因就在于写入数据库的时间比起抓取解析的时间要慢的多.所以就开始出现了性能瓶颈.通过分析，是写入数据库的性能跟不上抓取解析的性能.那么怎么样才能让两边的速率达成平衡呢？没错，就是前面前面说过多次的消息队列.

从名字上来看，我们就能知道这是一个传递消息的队列.通过分布式的消息队列服务，我们可以用来存储进程间传输的消息，为分布式部署的不同应用之间或者一个应用的不同组件之间提供基于消息的可靠的异步通信服务。消息被存储在高可靠、高可用的消息队列中，多进程可以同时读写，互不干扰。使用消息队列，用户可以在执行不同任务的应用程序的分布式组件之间传递信息，既不会丢失消息，也不要求各个组件始终处于可用状态。队列在数据发送端以及数据接收端之间起到缓冲作用。这样，在数据发送端的工作速度快于数据接收端的情况下，队列可解决因此而产生的问题。

  
同时，消息队列还可以解决原来构架中另一个存在的风险，那就是原来的模块之间存在传递数据的关系，万一其中某个模块出现了问题，那么就会导致蔓延到整体，最终出错停止.在爬虫架构中，模块与模块之间只需要数据交换，不需要太多逻辑上的关系.这种情况我们就可以把消息队列当成一个消息中间件.模块之前的数据传递通过中间件来完成.这样的话，如果任意模块出现问题，也只会影响到自身，不会蔓延到整体程序.

现在市面上的消息队列有很多，其中比较热门的有[rabbitMQ](http://www.rabbitmq.com),[kafka](http://kafka.apache.org/),[redis](https://redis.io/),[nsq](http://nsq.io/)和各大云平台自己开发的消息服务中间件，例如腾讯云的[CMQ](https://www.qcloud.com/document/product/406),阿里云的[MQ](https://help.aliyun.com/product/29530.htm)，AWS的[SQS](https://aws.amazon.com/cn/sqs)等等.

消息队列有这么多好处，那么我们怎么才能应用到我们现在的爬虫中呢，照例，我们先来了解一下消息队列是什么.

* 节点：指的是消息队列中用以处理数据的一段程序或者脚本。
* 生产者：指的是传递给队列数据的节点。类似于现实社会里面的生产商，他们通过生产物品来满足消费者的需要.
* 消费者：指的是通过向队列询问数据的节点.类似于现实社会里面的顾客.
* 消费信息：消费者每获取一次消息队列上的数据就称之为一次消费
* 队列模型：用以区分是消费者主动请求得到数据还是消费者被动的获取到消息队列传递来的数据。
  * 队列模式是消息生产者生产消息发送到queue中，然后消息消费者从queue中取出并且消费消息。消息被消费以后，queue中不再有存储，所以消息消费者不可能消费到已经被消费的消息。Queue支持存在多个消费者，但是对一个消息而言，只会有一个消费者可以消费.
  * 主题模式是消息生产者（发布）将消息发布到topic中，同时有多个消息消费者（订阅）消费该消息。和队列模式不同，发布到topic的消息会被所有订阅者消费。

在队列模式下，爬虫的工作模式大抵是这样的.抓取解析模块将数据提取出来后，发送请求给队列.而在主题模式下，存储模块通过订阅了topic来获取消息队列主动推送的有关抓取解析模块传递到消息队列的数据.消费信息后，两种模式都需要删除已经被消费过的消息，以便用来保证该数据不会被多次重复使用.

那么，加了消息队列后整个爬虫的组件的流程图是这样的：

![](/images/8.png)

从这张图中就能看出，子模块之间没有任何关联，全都是通过消息队列进行相互链接，这样就实现了模块之间的相互隔离。这样即使其中一个模块出现问题，那也不会蔓延至整体。最多只是爬虫因为没有数据而停滞。不过我们可以通过布置多个节点来防止消息队列上消息队列为空这个问题.




