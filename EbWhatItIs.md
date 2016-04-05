


大家都知道当年[IBM的"深蓝"](http://zh.wikipedia.org/wiki/%E6%B7%B1%E8%97%8D)和世界国际象棋冠军下棋的事情吧。 在那个例子里有三方参与：
```
1.  IBM 的深蓝   -- 通过人工智能  决定如何下棋
2.  国际象棋冠军   -- 他也是通过 “人工智能”  决定如何下棋
3.  国际象棋规则。  本身
```


# 恶狼战役其实就是一个棋局！ #

  1. 其战斗双方就是两个 “指挥决策进程” （由玩家自己编写）
  1. 我们的erlbattle 主程序就是维护了一个“棋局” 游戏规则
    * [EB:世界规则](ErlBattleRule.md)
  1. 恶狼战役是程序员和程序员的较量，是智慧和智慧的较量；
  1. 恶狼战役不是一个人机对战游戏，是一个 机机对战游戏。

## 解释 ##
发起: [关于恶狼战役的一个形象的比喻](http://groups.google.com/group/ecug/browse_thread/thread/1c2f1d860946300e) <sup>ECUG~erlang中文用户组 | Google 网上论坛</sup>

  * 主战场规则开发是比较简单的，因为规则本身很简单。为了保证游戏的公平性，这个规则还需要比较稳定。 不能变来变去。 就像国际象棋的规则几百年不变，才能够吸引全世界人民参与。
  * 而指挥决策程序如何写，完全是未来参与者的水平的体现了。 可能是一个100行的代码，也可能是一个20万行的程序，spawn 出10000个进程。 这个游戏规则和官方都不管。
  * 恶狼战役不是一个人能够参与的游戏，不是一个实时战略游戏，，计算机控制方会高速运算，战场节拍会在100毫秒左右进行运转。 （这能够挑战决策程序的并行运算效能）。
  * 玩家的智慧全部体现在你自己编写的“指挥决策进程” ， 要在战斗中获胜，你要考虑如何从侧翼甚至背后进攻对手，能够在局部地区多个角色攻击对方一个角色。 保护自己的受伤的部队，让他不被攻击，同时还能输出攻击。当敌人受伤部队开始逃跑的时候，如何追击。。。。。 会非常有趣。 希望大家能喜欢。


# Discuss #
[恶狼战役](ErlBattle.md)世界由学习者发起，也必然由学习者不断发展下去!

相关讨论::
  * [EB:路线](EbRoadMap.md) <sup>设计EB各个版本的代号</sup>
  * [EB:项目进化和开发过程](EbNowPath.md)<sup>eb0</sup>当前计划
    * [EB:无限畅想](EbUserSuggestion.md) <sup>在这里涂鸦，提出你的想法，畅想无疆</sup>