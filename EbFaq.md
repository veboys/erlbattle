

# EB世界的讨论 #
根据不同方向的论题进行分类和排序:
  * EB设计架构相关 `Q0-*`
  * EB发展运营相关 `Q1-*`
  * EB游戏规则相关 `Q2-*`
  * EB开发接口相关 `Q3-*`

## 有关EB设计架构的讨论 ##

### Q0-0: 战场应该可以定制 ###
Q0-0: 战场应该可以定制，如果日后支持多战队的话，10\*10 就不够了 ~ ZoomQuiet
> 开发计划就是1对1的战场，多方混战就容易大家都不动，等别人两败俱伤后再动的情况 ~ 老范

### Q0-1: 需要一个战斗平台来跑战斗吗？ ###
Q0-1: 需要一个战斗平台来跑战斗吗？
> 不需要。 大家都是下载源代码，在自己的机器上跑。 你可以编写你的军队，然后让他和任何一个对手进行挑战。  ~ 老范

### Q0-2: 游戏平衡 ###
Q0-2: 游戏平衡是个很关键和复杂的问题，法术之类的初期不应该考虑，只考虑移动和攻击。
> 同意, 我们的战士现在只能向前走,转身, 向后退, 以及朝前砍.  ~ 老范

### Q0-3: 为什么只有左右前后攻击和移动 ###
Q0-3: 现在只有左右前后攻击和移动,为什么不添加上 移动{左前 3秒, 右前 3秒, 左后 5秒, 右后5秒} 和攻击{左前 4秒, 右前 4秒, 左后 6秒, 右后6秒}呢
> 主要考虑到一方面人在打架时，都会正面对着对手，很少斜着打。 另外也是考虑到游戏的平衡性和简单性。现在是暂定规则，未来是否改进规则，来让战斗更加精彩，需要等战场实际跑起来后再大家讨论。 ~ 老范


### Q0-4: 时间节拍相关 ###

#### Q0-4.0: 双方要如何确保进行的时间同步？ ####
Q0-4.0: 另外老范说明一下，red/blue要如何确保进行的时间同步？

> red 和blue 是玩家自己负责的决策进程。 如何编写完全由玩家决定。 子进程如果再spawn 出100个进程，主战场也不管。 唯一提供的一个接口就是命令通讯接口。
> red 和blue 不是以回合制的方式大家一起/或者发轮流指令然后进入下一轮，而是更像即时战略那样你需要快速的发布出你的指令。 战场在每个时间节拍计算一下最新的攻击和移动状况，然后取下一个命令（如果有的话）， 如果没有命令战士就原地等着。 这就解决了前面某个朋友问的关于长考是否会堵塞战场的问题。  你如果要长考，你自己去长考。 这个世界按照自己的节拍在运转。 可能你考虑的时间，对手已经将你砍死了。  未来大家做指挥程序时，务必要考虑时效。要在一个节拍中把下一步行动策略算出来。 ~ 老范


#### Q0-4.1: eb为什么不使用令牌模式授时 ####
Q0-4.1: eb为什么不使用令牌模式，由主程序向决策程序发令牌。决策程序收到令牌后开始计算自己下一步动作，然后在规定时间内告诉主程序？ ~zoomq

> 从令牌角度而言，系统会如下面方式运行：（大概这个意思）

  1. world clock 向战场程序发出令牌。
  1. 战场程序更新战场状态，后向决策程序发出令牌
  1. 决策程序在收到令牌后开始计算， 并在若干秒内完成决策，并向战场程序发出任务指令。令牌又转到战场程序
  1. 战场程序收到令牌后，更新战士动作
  1. 战场程序将令牌传回给world clock
  1. wolrd clock 休息若干秒后重复。

> 这种令牌模式有一个问题，就是要求“决策程序自律”， 必须收到令牌后才可以开始计算。 并且没有收到令牌不允许向战场程序发消息。 发消息的话，不能狂发，必须每个战士只能发一个。。。。

> 另一个问题是这样的一种令牌传递，让整个系统不再是一个并行的erlang 程序了，而是一个串行/准串行的程序。不能体现erlang 的优势了。

> 现在的算法是决策程序在被spawn 出来后，可以一直运算，随时发出指令。和主程序没任何关系。 主系统维护一个队列，从队列中取决策程序的指令。 互不影响，完全并行。

#### Q0-4.2:指挥节拍和节拍配置 ####
[老范观点:指挥节拍和节拍配置 - ECUG~erlang中文用户组 | Google 网上论坛](http://groups.google.com/group/ecug/browse_thread/thread/11411c3ab54dadd6)
```
时间:090718 14:15~14:33 
地点:in mobile
列席:ZQ+老范
```
议题:
  * 为什么当前回放中,都是每次只有一人在动?
  * EB时间节拍怎么调节?

记要:
  * ZQ 坚持eb应该分布化，指挥进程应该可以自由的在同一节拍中合理的调动任意数量的战士行动！
    * 老范解释:
      * 回放器不完善，没有展现出可能的同时行动
      * 命令"纸条"中的序列字段，大家没有理解
      * 战场守护，针对行动和战斗时的先后判定，在有同时之嫌时的随机引擎还没有完成
      * 所以，可以期待，完全吻合EB 世界规则的引擎和回放;
      * 对于战场守护读取两个指挥进程输出的 战场表 策略，其实是有空子可以利用的，期望文档及时完善并宣传出来
  * ZQ 强调EB时间节拍应该可以统筹分布式的两个集群组成的指挥官进程
    * 老范解释:
      * 当前不考虑
      * 已经在故事中 http://code.google.com/p/erlbattle/wiki/EbMaxStory 指出了，EB通过定时取"纸条盒"的方式来进行节拍的控制
      * 所以,其实是鼓励大家使用更好的程序,在更快的机器中,在EB战场节拍时间之内,完成更加智能的指挥!
> > > ~ 不过,当前的节拍已经是 一百万次计算 的间隔了,足够用了
  * ZQ 提醒,节拍和对战双方指挥程序应该可配置,方便大家开发和调试
    * 老范解释:
      * 的确没有考虑到,一般是 hacking 代码
      * 如果8.1 进行比赛的话,对于主办方来讲也忒不方便了,
      * 将扩展 start() 支持参数,接受节拍和对战双方的指定



## 有关EB发展运营的讨论 ##
### Q1-0: 这个是一个文字版的Rocode 项目吗? ###
Q1-0: 这个是一个文字版的Rocode 项目吗?

> 准确讲现阶段规划的连文字版都不是,就是一个黑盒子比赛. 把两方关进去,胜者活着出来. 未来我们会考虑将比赛进程记录日志. 然后请志愿者开发一个战斗的回放器,将黑盒子里面发生的事情用图形化的方式展现出来.  ~ 老范
Q1-1: 我也想加入，正在学习ERLANG，但时间不是很充裕。
> 没有关系，这个项目参与者时间都不是很充裕，目的是通过做一个有趣的程序，来学习ERLANG 编程。 如果你希望参加，可以到： http://groups.google.com/group/erlang-china/browse_thread/thread/aa49891cf89d2cf2  回帖。 我们就会帮你加入。你哪怕什么都不做，就是关注一下这个项目，我们也非常欢迎。  ~ 老范

### Q1-1: 好的东西一定要重复使用，这是我的想法啦 ###
Q1-1: 好的东西一定要重复使用，这是我的想法啦
> 现在还顾不上这个， 完全处于学些erlang 程序过程中，基本上每个语法都是一边查手册，一边编出来的。 希望尽开战场能够跑起来。 未来再不断优化。 比如这个图中有一个严重的问题就是2个决策进程和时钟程序会互相干扰，这个是一个简化的实现， 未来要解决的。 提到的耦合性过高的问题，也可以以后再想办法。
> 如果有个能够跑的系统，大家也更容易理解恶狼战役是个什么东西，更容易参加。 这是目前的当务之急。 现在通过图，文字还是有很多含糊的地方。  ~ 老范

## 有关EB游戏规则的讨论 ##

### Q2-0: 机器不同是否就会不公平 ###
Q2-0: 如果比赛的时候，一方用的是8核的机器， 一方是普通机器，是否就会不公平
> 这是一个理解差异。 我们的双方是运行在一台机器上的。大家都可以下载代码后，自己运行比赛，不需要公共服务器来做比赛。 我们也会进行官方的比赛，并将比赛结果公布。 ~ 老范

### Q2-1: 战场是否会被一个喜欢长考的将领堵死? ###
Q2-1: 战场是否会被一个喜欢长考的将领堵死?
> 不会. 两支部队都是独立进程,不会互相影响. 长考的将领会会丧失行动的先机. 当然后发制人也是一种策略. ~ 老范

### Q2-2: 战场规则制定的决策过程如何？ ###
Q2-2: 战场规则制定的决策过程如何？
> 类似于足球赛，如果规则使得意大利队一直能够通过乌龟战术赢得冠军，比赛非常无聊的话，就必须改变规则。对于现有的规则的修订未来需要项目参与者的投票，另一方面需要通过实际战斗，发现规则是否影响了鼓励精彩的战斗。  ~ 老范

## 有关EB开发接口的讨论 ##
### Q3-0: 为什么战场状态表是named ets ###
Q3-0: 为什么战场状态表是named ets ,而双方的指令队列是 none-named ets
> named ets 表让所有知道名字的人都能够看到该表的内容。 在系统中，战场状态表是named; 因为当你面对你的对手时，你通过他的动作（比如举起刀子， 或者开始左转）就能够猜到他下一步的动作。这个信息可以开放出来让对手看到，以作出相应的反映。而对手心中想着下一步该如何动作这个是看不到的，大家都没有读心术。因此指令队列是 none-named.  ~ 老范

### Q3-1: 建议使用一个单独的module来管理所有的ets表 ###
Q3-1: 建议使用一个单独的module来管理所有的ets表， ~ Max
> 我考虑是由负责的进程来管理对应的ets 表。 比如时钟由timer 负责。 而战场表，以及战场任务队列表 由战场负责。 不大倾向于用公共模块。用公共模块除了从”对象封装“ 角度而言美感不足外， 还有一个问题是就变成必须使用public 表。 public 就得每个程序自律不能去改他。比如决策进程如果可以改战场表的话， 就乱套了。毕竟未来决策程序是让公众开发的，我们不能保证每个公众都自律。
> 现在timer ， 战场表 都是使用protected named 表。 这样其他相关进程在需要的时候都能自由访问， 但不能修改。
而任务队列表是 none-named protected. 而主程序只会把蓝方表的PID 传给蓝方， 红方PID 传给红方。这样双方都不能偷看别人下一步安排。 当然决策程序如果自己管理的好的话， 也可以选择不看这个系统维护的任务队列表。 决策程序如何做，我们不管。 ~ 老范


### Q3-2: 为什么要明确的分red和blue呢？ ###
Q3-2: 为什么要明确的分red和blue呢？
> 这个在FAQ 中提到了。 其实也是大家对于恶狼战役的一个非常大的理解差异所在。 恶狼战役未来就像下棋一样，都是2方进行的。 不是多方进行的。如果未来大家编了很多指挥算法程序的话， 我们就像世界杯的小组赛一样进行循环积分赛。 战胜最多对手的获胜。（这个避免某个程序专门克制某个程序的情况出现）。
> 多方参与最大的坏处会造成大家都希望鹬蚌相争，渔翁得利； 都消极避战情况的出现。 因此恶狼战役是标准的1对1.  一边是红方，一边是蓝方。
> 现在为了测试用开发了一个叫做FeardFamers 惊恐的农民， 和EnglandArmy 英国卫兵  两个算法就是两个决策进程。各自有各自的指挥策略。 FeardFarmers 已经开发完成，因为他其实就是什么都不做。 EnglandArmy 还没完成，他的策略就是一直朝前，碰到人就砍。 就像英国人那样耿直，不会拐弯。 ~ 老范


