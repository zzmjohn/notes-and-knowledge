overview
========

terminology
-----------

- expected failure. When a test fails in an expected way. 这可能是因为实现还不
  充分.

- unexpected failure. When a test fails in a way we weren’t expecting. This
  either means that we’ve made a mistake in our tests, or that the tests have
  helped us find a regression, and we need to fix something in our code.

- user story.

  * A concrete instance of user's interaction with the application. It
    describes how the application will work from the point of view of the user.

  * It is used to structure a functional test.

  * A user story has to be a story. So it phrases a complete session of user
    interaction with the software, in a natural language.

- microstepping. test/code cycle must be tiny.

- SUT. system under test.

- UUT. unit under test.

- AUT. application under test.

TDD
===
- TDD 是一种软件开发方法论, 它描述的是一种基于自动化测试的研发流程. 在这个流程
  中, 需求转化为测试用例, 功能实现以通过测试用例为主要标准. 从而, 用测试来 "驱
  动" 开发.

- TDD 的思想, 最重要的特点是将测试提前到实现之前. 而不是先实现, 再根据实现来写
  测试. 通过测试代码来具体化需求, 将需求呈现为一个个可以检验的指标. 也就是说,
  测试定义软件整体的行为和各个模块的 API, 以达到驱动软件整体以及各个模块的设计
  和实现的效果.

  In TDD, The tests tell you what to do, what to do next, when you are done.
  They tell you what the design is, what the API is going to be.

- TDD 的软件开发方法就像物理学中研究事物的方法. 在物理学中, 我们认识事物的
  唯一方式是通过观测和实验. 如果对一个事物的观测结果或实验数据符合我们对某种
  已知物理概念的认知 (数学模型), 那它就是什么.  我们从来不关心一个事物 "所谓
  的本质" 是什么, 因为观测到的才是有效的, 我们从来无法确知也无需关心那 "本质".
  (如果一个模型能精确地描述一个物理现象, 那这个模型就是好的, 就代表了我们对
  该物理现象的认识.)

  在 TDD 中, 我们通过测试代码构建对软件的行为预期, 这就是在构建软件的行为模型.
  能通过测试的应用就是符合行为模型的应用, 就是符合用户需求的应用. 这种思路的
  一个重要推论是, 能通过测试的代码自然具有合适的内部结构 (本质). 行为决定本质,
  行为等价于本质. 对行为的约束会自然形成恰当的内部结构. 所以软件行为的定义和
  验证的重要性是很高的, 然后才是设计实现.

why TDD
-------

- TDD 强制先设计, 再实现. 先考虑怎么用, 再考虑怎么做. 这是由于要先写出测试代码,
  所以程序员要设计程序的整体行为 (即程序如何和外部交互), 以及各个模块的 API (即
  模块之间如何交互), 然后才去填充实现.

  这种方式, 有助于达到一个使用起来更自然更合理的设计.

- 在写测试用例定义模块的行为的过程中, 有时候还会发现一些功能设计不合理的地方,
  这样及时纠正了功能设计, 不用等到功能实现完了再返工.

- 接上, 这种流程强制程序员写出易于测试的代码. 因为必须已经知道相关代码要怎么
  测试了, 已经知道它会怎么去使用了, 才会去写代码本身. 相反, 如果实现之后再写
  测试, 有可能代码本身是不易于测的, 因为没有测试代码去做规范.

  而易于测试的代码自然带有一个更有价值的属性: 低耦合. 否则它不可能易于测试.

- 在写功能代码前先写测试代码让写测试代码这件事更加 stimulating, 因为测试代码
  成为了开发过程的 guideline, 是个必要的成分. 如果完成功能代码后再写测试代码,
  那么写测试这件事更让人觉得这是额外的一项工作, 只是为了保证 *以后* 去检查
  regression bug, 而对当下并没有用, 因为代码已经手工测试过了.

- Better code quality. 与前一点相关, TDD 在帮助实现更好的设计时, 显然也同时帮助
  实现更好的代码质量. 因为要求可测性, 因而有清晰的 API 和功能行为. 这样的功能
  模块达到 high cohesion low coupling. You can bind it to the rest of the
  project as easily as you can test it.

- TDD can lead to more modularized, flexible, and extensible code. This effect
  often comes about because the methodology requires that the developers think
  of the software in terms of small units that can be written and tested
  independently and integrated together later. This leads to smaller, more
  focused classes, looser coupling, and cleaner interfaces.

- TDD 有助于更有目的地做事, 构建出最小可用的实现, 提高开发效率, 避免不必要的
  复杂度. -- YAGNI. You ain’t gonna need it! Avoid the temptation to write code
  that you think might be useful, just because it suggests itself at the time.

- TDD 时, functional tests and acceptance tests 可以做需求文档的具体实例 (user
  story). 而所有测试代码, 尤其是 unit tests 可以作为 API documentation.
  Sometimes, if you forget why you’ve done something a particular way, going
  back and looking at the tests will give you the answer.

- 先写测试再写代码更有趣, 因为是先构建问题 (test failure), 再解决问题 (test pass)
  的过程.

- TDD 似乎能大幅减少 debugging 时间.

- The most magical thing about TDD is: By enforcing a rigorous suite of tests,
  the right algorithm/implementation is just incrementally being derived out of
  those tests. It doesn't mean we don't plan things, design things, do
  architecture. It does mean we got a tool to support incremental development.

- TDD 的功能测试和单元测试还可以在 interface review 上发挥作用. Interface
  review 比 code review 重要.

- 这种小步伐的 test/code cycle 还有助于 keep development progress. 注意到所有
  的 development expectation 都在 functional tests and unit tests 中得到记录.
  如果忘记上次开发到哪里了, 只需跑一轮测试, 哪里不通过, 就知道开发到哪里了 (因为
  每次一小步, 已经实现的代码部分都相应地测试通过了.)

- 测试用例可以作为 TODO 来使用. 当我们在开发一个模块时, 可能突然想到其他地方需要
  一些修改, 那就可以迅速地在相关测试代码中添加一个 placeholder test case (make
  it fail). 然后 test failure will remind you of unfinishing works.

questions and concerns
----------------------
- microstepping 所要求的每次 test/code 循环每次只解决一点问题就测试太繁琐了吧?

  * 一开始需要这么做, 是为了强化流程. 当熟悉之后, 可以适当灵活一些.

  * 但一定要抑制不写测试就写代码实现的冲动. 要保证 test/code/test cycle.

  * think ahead, test ahead, implement, test again.

- 每行代码即使是非常 trivial 的操作、函数也要测试?

  * 如果不想这么严格, 其实可以放松一些. 即不绝对测试所有代码.

  * 但是, 这么做是有好处的. 首先, 强制一套所有代码都要测试的规矩, 有助于在复杂
    的实际应用中, 不让任何本该测试的、看上去十分无辜的代码被忽略, 最最终成为
    那最脆弱的一环.

  * 其次, 在实际项目中, 随着产品的迭代, 一个简单的逻辑可能逐渐变得复杂, 如果
    一开始因为简单所以不写测试, 那么要如何决定何时这个逻辑复杂到该写测试代码
    呢? 这个界限可能是很主观、比较不可靠的, 并且需要花时间考虑要不要加测试.
    甚至更糟的是, 由于一开始没有相应的测试, 就懒得写, 一直拖着, 最终变成大麻烦.
    所以, 即使是最简单的逻辑, 从一开始就写上 placeholder test case, 是最稳妥
    的做法.

  * 最后, 既然逻辑 trivial, 测试 trivial, 何不少说废话直接写了得了呢?

- TDD 不是不注重设计. 相反, 前期对需求的分析和功能设计的仔细考虑并没有任何改变.
  只有当相对宏观的设计已经想清楚了, 才开始将需求和设计具体化为功能测试用例.
  此时, 才开始 TDD 的流程 (即开始开发).

  如果对于能通过测试的实现 (green), 如果不够清晰合理, 及时 refactor. 不是说
  第一次实现时只要通过测试即可, 如果能一次性实现好, 当然最好. 只是说, 不需要
  强求一次性达到最佳实现, 快速做好第一版实现, 如果需要 refacor, 就去 refactor.

- TDD 与创造性和功能的一般性. TDD 强调 YAGNI, 鼓励 (合理设计的) 代码仅仅能够通
  过测试即可. 考虑到企业业务逻辑快速和频繁变化的 情景下是合适的. 但不要将这个思
  路绝对化. 该一般化的时候就要一般化, 该创造性地实现一个牛逼玩意儿时还是要发挥
  最大的创造性.

  或者说, TDD 不该抑制创造性和更好的设计.

- TDD 及自动化测试能否做好要看多方面的因素.
  
  * 个人因素: 愿意尝试新思路、新的做事方法, 学习的意愿和学习的能力, 能坚持做下
    去的毅力, 面对问题能够冷静认真地去分析和解决、而非浮躁和草率地处理.

    TDD 是先写测试再写实现的. 这要求作者必须对功能的实现细节先规划好. 一开始这
    是不适应的, 并且对相对复杂的模块会比较困难. 这要求作者能够对功能如何实现有
    良好的把握.

  * 团队因素: 相信 TDD 和自动化测试有潜力带来价值, 解决问题; 愿意花时间去尝试,
    即使最终效果可能不够理想, 包括上手阶段的学习所花费的时间与平时研发所花费的
    额外时间.

  * 条件因素: 包括现有工具集是否丰富、能否满足测试需求, 是否需要自制一些工具.

- TDD 及自动化测试是有一定的学习曲线的. 它需要至少在以下方面进行深入:

  * 理论学习: 学习各种测试的概念和方法, 学习 TDD 方法论.

  * 工具: 各种自动化测试工具, 包括但不限于: 单元测试 library, mock library,
    假数据生成 library, 浏览器操作工具 (例如 WebDriver, Selenium).

  * 框架源代码: 当 SUT 需要使用框架时, 可能需要对框架的底层 API 和执行逻辑有一
    定了解, 以保证单元测试的独立性.

  * 实践: 大量的实践, 并能够冷静地解决实际中遇到的问题和挑战 (因为一定会遇到很
    多很多问题). 所谓 "纸上得来终觉浅，绝知此事要躬行". 只有在实践中遇到足够多
    种的情况, 解决了足够多种的问题, 才能说 "熟练".

- TDD & 自动化测试与研发效率的问题.

  * 在学习和上手阶段, TDD 带来的是短期研发效率的下降, 这是必然的. 学习任何新技
    能都会有相应的影响. 这也是团队和个人最容易放弃的阶段.

  * 根据我个人的经验, 我原来每天可以写 300+ 行未经测试的 python 代码. 当使用
    TDD 比较熟练之后, 后每天的总代码量可达到 600-700. 这里面大约 50-60% 是测试
    代码. 所以从纯代码量角度来看, 研发效率并没有下降或仅有轻微下降.

  * 然而这带来的价值是: 所有这些代码都是测试过的, 可用的. 注意原来是每天 300+
    行未测试过的代码, 而现在是几乎同样 (或略少) 数量的代码, 却全部是经过
    UT/IT/FT 测试通过的. 它们消除了绝大部分原来需要花在单独手工测试、集成、调试
    上的时间. 而且原来手动测试的覆盖度远不如自动化的测试集全面. 所以从整体效益
    来看, 是提高的.

  * 并且注意到自动化测试集是可以在多次迭代中重复使用的. 这对回归测试的效率是很大
    的提升. 长期效益是累计提高的.

  * 此外, 全面的测试有助于在研发早期就发现和解决 bug. Bug 在越早的研发阶段发现,
    越早解决解决, 整体成本越低. 等到上线后才发现问题, 修复成本会变得很高.

- 坚持进行测试, 坚持 TDD. 就像做一切事情, 贵在坚持, 难在坚持.

TDD workflow
============

general and detailed workflow
-----------------------------
.. |tdd-workflow| image:: tdd-workflow.png

- in general:
  
  * test/implement/test[/refactor] cycle or Red/Green/Refactor cycle.

  * Working incrementally and step-by-step, with each of them should be small.

- detail (Double-Loop TDD).

  |tdd-workflow|

1. 将需求具体化为用户故事, 将用户故事转化为 FT. 执行 FT 以保证测试不通过. (Red)

2. 写出最小可通过 FT 的功能实现.

   在实现功能时, 首先进行架构设计. 考虑该功能需要哪些模块, 有哪些功能层. 然后
   一个一个功能模块去实现. 对每个模块的实现, 遵循以下子步骤:

   1. 考虑这个模块要如何设计, 它的 API 如何, 它的结构如何, 它的行为如何. 写下
      相应的 UT 去定义这些 API 和 API 的行为. 执行这些 UT 以保证测试不通过. (Red)

   2. 写出最小可通过 UT 的模块功能实现. 执行 UT 以保证测试通过. (Green)

   3. 考虑代码实现是否需要优化和重构. 若需要, 进行重构, 优化代码质量. 执行 UT
      以保证测试通过. (Refactor)

   在所有相关模块都实现完成, 并且所有相关单元测试都通过了情况下, 执行 FT, 检查
   FT 是否通过. 若否, 再次深入实现层, 按照以上子步骤调整模块实现或者添加新模块.
   若 FT 通过, 则该功能实现完成. (Green)

3. 考虑功能实现是否需要架构设计方面的优化和重构. 若需要, 进行调整, 并返回第 2
   步实现调整后的功能. 最终保证所有 FT/UT 全部通过. (Refactor)

关于步骤的说明
--------------
- 以上步骤其实强调了两个概念: "层" 和 "循环".

  * 在宏观的功能层, 我们有功能测试来定义实现的模样. 在微观的模块层, 我们有单元
    测试来定义实现的模样.

  * 无论是宏观的功能层还是微观的模块层, 开发都是通过 Red-Green-Refactor 这个循
    环来推进的.

- 在上述第 2 步中, 实现一个功能所需的各个模块时, 具有两种思路:

  * 按由内层模块至外层模块的顺序进行 (Inside-Out), 也即先实现数据层, 再实现
    展示层.

  * 按由外层模块至内层模块的顺序进行 (Outside-In), 也即先实现展示层, 再实现
    数据层.

  这实际上是 TDD 的两个所谓 "门派", 即 London School TDD and Detroit School
  TDD.  但无论是 Outside-In 还是 Inside-Out, 这些都是方法. 我们的目的是达成一个
  合理的设计和优质的实现.  在实践中, 这两种思路各有其用途, 没有必要坚持只使用由
  外至内的顺序或者反之.  我们可能会 out-in, in-out, out-in, etc.  等一系列过程,
  最终达到一个很好的结果. 这是一个灵活的随机应变的过程. 即 agile 的本质.

  In large, enterprise solutions, where parts of the design come from
  architects (or exists upfront) one might start with "London style" approach.
  On the other hand, when you face a situation where you're not certain how
  your code should look (or how it should fit within other parts of your
  system), it might be easier to start with some low-end component and let it
  evolve as more tests, refactorings and requirements are
  introduced.[SETDDOutsideInInsideOut]_

- 关于怎么样的实现是 "最小" 的实现. 我并没有深究这个问题. 因为我不太认可我看
  过的 TDD 书籍中所推崇的那种 "minimal code" 做法. 在实践中, 我只是依据 UT 去
  自然地去写出我认为是该模块的最佳实现 (并配合重构).

* FT 描述的新功能需要在软件的哪个部分添加功能实现, 就在这个部分中写单元测试和
  进行实现. 每个部分所用的语言可能是不同的, 所用的单元测试框架也可以是不同的.
  注意 FT 的实现与具体的单元测试 (和实现) 是独立的.

* 关于 FT 的执行. 由于 FT 执行起来可能比较慢 (要调用浏览器等), 为了提高 TDD
  cycle 速度, 可以根据具体情况选择只执行与当前功能相关的 FT, 不执行全部 FT.  将
  执行全部研发阶段的 FTs 的任务留给构建服务器去完成.

* 在研发过程中的尝试性设计与实现阶段. 对具有难点的新功能的设计和实现, 往往难以
  一次性就作出正确的决策, 或者需要一些尝试与原型实现. 在这种情况下, 没必要严格
  执行 TDD 流程, 同时修改代码实现和测试用例是允许的. 甚至可以暂时不使用 TDD. 当
  设计与实现已经有了一个可行的基本思路后, 再进入 Red-Green-Refactor 循环.

* 在实践中, 可能存在从测试用例 (设计) 至实现, 再由实现扩展测试用例 (设计). 这样
  交替的、相互影响的过程.
  
  有些时候在写模块的单元测试来设计模块功能时, 可能我们写几个测试用例后, 就可以
  基本构建出实现的结构. 然后就开始了实现. 实现过程中, 可能会出现很多灵感, 然后
  实现的功能已经比较完善了, 原有的测试用例不够覆盖实现中的各种情况, 那就需要
  反过来根据实现去补充测试用例.

  但前提是这个完善的实现是恰好的、符合需求的, 而不是过分复杂的. YAGNI.

* 在完成功能实现、执行测试校验结果时, 警惕一次性通过所有测试的代码实现. 因为更
  有可能是某些环节出了问题, 导致而测试没有生效.

- 关于安全地重构.
  
  * 当重构时, move step-by-step, from working state to working state. Being the
    testing goat, not the refactoring cat. Our natural urge is often to dive in
    and fix everything at once... But if we’re not careful, we’ll end up like
    Refactoring Cat, in a situation with loads of changes to our code and
    nothing working again.

  * When refactoring, the code should starts with working state, then move
    incrementally to another working state. 步伐尽量可控, 过程中每一步都要保证
    测试通过, 不要一次性做一大堆修改然后扯着蛋.
  
    The step-by-step approach, in which you go from working code to working
    code, is really counterintuitive. 甚至中间的一些 working state 极其错误, 完
    全不合理. 但这完全是为了不破坏已经建立的局面, 然后一步一步向更好的局面发展.

  * You can begin refactoring only when you know you are safe to refactor. 也就
    是说, 例如我们已经完成一个功能还没有开始新功能的开发, 或者至少我们现在位于
    working state. 不要在半截上开始 refactor, 此时应该先记下稍后需要 refactor.
  
  * Don’t refactor code against failing tests, except for the test you are
    currently working on.

Outside-In TDD
--------------
- Outside-In TDD 的思路是由外至内地去实现 -- (由宏观需求触发) 交互/展示/UI 层,
  view/controller layer, model layer 等.

- 为什么要由外至内的顺序去实现? 因为内层该具有什么样的 API 本质上应该由外层需要
  如何使用来决定. 也就是说, 每个外层都为它所依赖的内层提需求, 而每一个内层的实现
  都完全是为了满足外层的使用需求而实现. 这样更容易达成一个恰好够用的设计 (YAGNI).
  相反, 如果按照最直观的实现思路, 即先内层后外层的实现, 内存 trying to
  anticipate the usage pattern, trying to anticipate the upper layer's
  requirement, 这样可能预测出错, 需要返工.

- Outside-In TDD is also called "programming by wishful thinking". We start
  writing code at the higher levels based on what we wish we had at the lower
  levels, even though it doesn’t exist yet.

  Actually, any kind of TDD involves some wishful thinking. We’re always
  writing tests for things that don’t exist yet.

- Outside-In TDD 必须保证 test isolation. 使用 mock 将被测功能与它的依赖独立开来.
  在写这种 isolated test case 时, 它会自动 drive 我们将功能按照不同层去考虑, 将
  不属于被测功能层的内容解耦合至其他模块.

  Isolated test 只测试该功能层的逻辑, 这包括它自身的 API 以及依赖调用. 不测试任
  何其他层的逻辑和 side effects. 并且这种该测试什么、不该测试什么实际上由 mock
  来强制执行了, 因为依赖全部被 mock 掉了, 没办法去测试其他层的逻辑和副作用.

- 我们可以认为一个功能的多个实现层是相互协作的关系, 即互为 collaborator.
  每个 collaborator 提供的 API 就是它与其他 collaborator 之间的 contract.
  Whenever we mock out the behaviour of one layer, we have to make a mental
  note that there is now an implicit contract between the layers, and that a
  mock on one layer should probably translate into a test at the layer below.

- 使用 Outside-In TDD 时, 需要尽量保证测试代码对被测功能的细节访问仅限于其他
  层 API 部分. 避免太多耦合. London-school TDD routinely provides feedback
  about whether each unit's usage is awkward under real-world conditions.

- Outside-In TDD 的缺点:

  * Outside-In TDD 的最大缺点是为了保证单元测试的独立性, UUT 的测试测试, 必须要
    清楚 UUT 的底层依赖是什么, 以及 UUT 是如何使用这些底层依赖的 (需要 mock 掉
    这些集成点). 这导致测试代码不可避免地与被测模块的实现细节有一定的耦合. 从而
    提高了重构的成本.
 
  * Outside-In TDD 让研发人员关注于对用户直接可见的功能部分, 这样可能会忽略其他
    不直接对用户可见、却对系统完整性仍然至关重要的功能部分, 例如安全性考量. 因
    此, 研发人员需要在设计时考虑全面, 提醒自己那些隐藏的功能点.

Inside-Out TDD
--------------
- Inside-Out TDD. the natural way most people intuitively work before they
  encounter TDD. After coming up with a design, the natural inclination is to
  implement it starting with the innermost, lowest-level components first.

- It feels comfortable because it means you’re never working on a bit of code
  that is dependent on something that hasn’t yet been implemented. Each bit
  of work on the inside is a solid foundation on which to build the next
  layer out.

- The most obvious problem with inside-out is that it requires us to stray
  from a TDD workflow. Instead of solving the most imminent testing failure,
  we decide to ignore that and go off to the lowest level to build from
  there (with test/code cycle).

- Inside-Out may build inner components that are more general or more capable
  than we actually need, which is a waste of time. It may build inner
  components' APIs that is incompetent for upper layer's use. Even worse,
  the lower level components might not even solve the upper layer's problem.

TDD on deployment
-----------------
- TDD 的思路还可以应用于服务器应用部署方面 (非容器化的方式). 一步一步地配置,
  work incrementally, make one change at a time, and run your tests frequently.

  When things (inevitably) go wrong, resist the temptation to flail about and
  make other unrelated changes in the hope that things will start working
  again; instead, stop, go backward if necessary to get to a working state, and
  figure out what went wrong before moving forward again.

  Don't fall into the Refactoring-Cat trap on the server.

About prototyping
-----------------
- prototyping: 尝试和学习一个新的工具, 设计一个新的解决方法时, 可能需要一些
  表达基本思想的原型代码. 这就是在做 prototype. 在 TDD 中也称为 spike.

- 在做原型时, 完全可以不管 TDD 或只有必要的测试代码, 纯粹尝试性的 try if it
  works as expected.

- 在将 prototype 重新整理为系统化的设计和实现时 (de-spike), 再认真地 TDD.

test classifications
====================

- The functional tests are driving what development we do from a high level
  (outside), while the unit tests drive what we do at a low level (internal).

- The functional tests are the ultimate judge of whether your application works
  or not. The unit tests are a tool to help you along the way.

- Functional tests should help you build an application with the right
  functionality, and guarantee you never accidentally break it. Unit tests
  should help you to write code that’s clean and bug free.

functional test (FT)
--------------------

- functional test, 在 TDD 只关注于研发阶段, 这里主要指的是研发阶段的功能测试,
  这不同于集成测试或系统测试时的功能测试.

- FTs test how application *functions* from the user's point of view.

- The main point is that these kinds of tests look at how the whole application
  functions, from the outside, from end user's point of view, rather than from
  the programmer's point of view.

- 因为 FT 具有最终的视角, an FT can be a precise specification for your
  application. It tends to track what you might call a *User Story*, and
  follows how the user might work with a particular feature and how the app
  should respond to them.

- When creating a new FT, we can write the comments first, to capture the key
  points of the user story or specification.

- 即使需求通过 specification 的形式呈现, 一组功能测试本身必然是基于某个
  具体的 user story 来呈现和校验的 (user story 是 specification 的具体呈现). We
  use comments to explain the User Story in our functional tests, by forcing us
  to make a coherent story out of the test, it makes sure we’re always testing
  from the point of view of the user.

- 功能测试中可以测试 style design 是否按预期加载, 但不严格测试 style 本身.
  例如对前端页面, 测试方法可以是: 大致地测试一下某个页面组件是否在预期位置附近,
  以确定 style 文件被加载 (smoke test for css file loading).

- 注意 TDD 使用的 functional tests 是不同于集成测试或系统测试中的功能测试.
  
  * TDD 时的 FT 目的是 drive design, testing design during development.
    而集成和系统测试的目的就是测试, 而且是对开发完毕后的软件进行测试.
    
  * TDD 时的 FT 必须执行迅速, 快速给出反馈, 若涉及 external services, 可以
    mock. 而集成测试和系统测试必须是在真实的服务上进行测试.

- 功能测试因为是从用户角度进行测试, 这样的测试应该尽量保证与 SUT 的实现细节
  相独立. 即黑盒测试. 然而, 由于这是研发阶段的测试, 在恰当的时候, 可以走一些
  捷径, 访问实现细节进行更方便、更高效的 baseline setup. 这需要根据具体情况
  分析决定.

design pattern
^^^^^^^^^^^^^^

- 如何组织功能测试?

  * 功能测试按需求点分测试文件, 按 user story 来组织每个文件中的测试. 每个 test
    file 中包含一个或多个用户故事组成的 test cases.
    
  * 在测试文件中若需要测试类, 可以按照用户类型去封装, 即对特定类型用户进行多个
    用户故事测试. 若只有一种用户类型, 则按照其他角度来分类.

  * 每个 feature 可能需要多个 user stories 从不同方面具体化. 对应于一个 test
    class 的多个 test method. 每个 test method 表达一个完整的 user story.

  * 对 index 页面, navigation bar 等的专门功能测试, 只需要测试个大概即可.
    因为它们属于交互框架部分, 所以实际上在各个用户故事的测试过程中, 对相关
    的具体内容都有涉及. 这样组织起来更有调理.

- 涉及外部的、不可控的系统时, 功能测试是否应该 mock?

  * 在研发阶段对外部服务集成的功能测试, 有两种方式: 1) 可以手动进行, 只测试是否
    通畅等, 在测试人员的测试中再详尽地做; 2) 确实去连接外部服务, 进行测试. 这样
    可能很慢, 或对执行环境、条件、时机等因素具有一定的限制.

  * 对于研发阶段的功能测试, 可能需要适时去与外部服务独立开来, 否则难以进行.
    这完全是为了方便研发而进行的.
    
    从概念上我们实际上是划分了可控的系统部分 (我们开发的系统), 与不可控的系统部
    分 (依赖的外部系统). 只对我们的系统进行测试.
    
  * 如果需要隔离外部服务, 则也需要 mock 与外部服务的相关集成点.

- An application's functional tests should tell the user story or covers the
  specification in an programmatical way. The specification can be made more
  explicit by comments etc.

- 功能测试代码应当尽可能与实现独立. 功能测试尽量不直接引用实现细节 (只检验
  实现). 它是从外部观测. 功能测试与所测试功能的实现理论上可以在两种不同的语言
  中写. 然而, 由于这是研发阶段的测试, 在恰当的时候, 可以走一些捷径, 访问实现细
  节进行更方便、更高效的 baseline setup. 这需要根据具体情况分析决定.

- functional tests 校验应用对外的功能, 只要应用的功能逻辑不变, functional tests
  的逻辑就应该是不变的.

- About testing on design and layout.

  基本原则: Don't test aesthetics in automated tests.
  
  这是因为: 1) 样式设计都是在静态文件中固定写好的, 这相当于常量的地位; 2) 对
  style 的测试容易比较 brittle, 需要经常修改; 3) 样式设计最好是由人类去辨别.
  
  但是, 进行某些基本的 style checking 还是可以的, 以保证比如静态文件正确加载,
  预期的效果大致达成. It is valuable to have some kind of minimal "smoke test"
  which checks that your static files and CSS are working.

  Try to write the minimal tests that will give you confidence that your design
  and layout is working, without testing what it actually is. Aim to leave
  yourself in a position where you can freely make changes to the design and
  layout, without having to go back and adjust tests all the time.

- 浏览器的响应相对于测试代码对浏览器的操作, 是一个异步的行为. 测试代码必须实现
  某种 polling 机制, 将异步转化为同步. 也就是说, 对于每个交互操作, 等待浏览器的
  响应出现、并进行检查后, 再进行下一步操作.

- UI Map.

  * Store all the locators for a test suite in one place for easy modification
    when identifiers or paths to UI elements change in the application under
    test.

  * 这样便于保持单一变量. 如果需要修改页面布局和 markup, 只需修改 UI map 中
    的值即可.

- Page object pattern.

  * Page objects are an alternative which encourage us to store all the
    information and helper methods about the different types of pages on our
    site in a single place.

  * The idea behind the Page pattern is that it should capture all the
    information about a particular page in your site, so that if, later, you
    want to go and make changes to that page—even just simple tweaks to its
    HTML layout, for example—you have a single place to go to adjust your
    functional tests, rather than having to dig through dozens of FTs.
    
    In other words, to stay DRY.

  * 当实现 page object pattern 时, 注意 page object 的定义只提供页面操作 (UI
    services), 封装 page-specific layouts and locators etc. 如果需要在 page
    object 提供的服务内部进行 checking/assertion, 仅限于为了封装和提供服务而进
    行的相关检测. No code related to what is being tested should be within the
    page object.
    
  * A page object does not necessarily need to represent an entire page. The
    Page Object design pattern could be used to represent components on a page.
    If a page in the AUT has multiple components, it may improve
    maintainability if there is a separate page object for each component.

integration test
----------------
- An integration test tests the interaction of modules, whether they give the
  expected result.

- 集成测试同样也可以 drive 模块的设计和实现.

- 在 TDD 流程中, 集成测试位于单元测试与研发阶段的功能测试之间, 它的主要作用
  是 provide a faster feedback cycle, and help you identify more clearly what
  integration problems you suffer from, 以打通各个层. 因其快速, 可以快速检验.
  这是功能测试不够合适的地方.

- 注意 integration test 测试的是一个服务/组件的各个代码模块之间的集成情况. 而不
  是跨服务、跨语言的测试, 那是 system test 的职责.[SOITExAPI]_

- 集成测试是必要的, 因为独立的单元测试只能测试模块本身的逻辑, 不能测试各个模块
  之间的集成是否通畅.

- ITs 与 FTs 在检测内容上会有一定的重叠, 这是正常的. 然而它们测试的目的, 范围,
  以及实现方式是不同的.

- Integration tests will try to drive the integration points to a minimum
  amount and in a consistent way. We should minimise the amount of our code
  that has to deal with boundaries, isolate our core logic and business from
  integration points (ports and adapters).

design patterns
^^^^^^^^^^^^^^^
- 集成测试的覆盖面. Integrated tests are most useful when starting at the
  boundaries of a system— at the points where our code integrates with external
  systems, like a database, filesystem, or UI components, then testing inwards
  -- towards your code.

- 当涉及与外部服务的交互时, 集成测试需要把这个 API mock 掉. 你只测试自己的代码,
  不测试你依赖的外部应用/服务.

  * 对于外部服务 API, 你需要做的是: 弄清这个 API 在各种情况下的输入和输出.  并
    根据这些不同的情况, 设计相应的测试用例来测试你的代码在不同用例中的应对情况.

  * 信任外部服务 will act according to its API specification, 如果涉及到外部
    服务的 API 封装和抽象层, 则信任这些封装和抽象. 同样地, 不测试这些封装和
    抽象, mock 掉, 只测试你自己的代码.

- 集成测试的组织形式:

  * 集成测试按照对服务/组件测试的不同测试角度来分类.

  * There should be at least one test case for each logical path from the
    boundary of your application.

- 在执行效率上 integration test 一般比 unit test 稍慢一些, 但也不应该太慢,
  需要至少能够比较快地执行, 提供 quick feedback.

unit test
---------
- Unit test verifies the correctness of the logic of a single module of your
  application.

- 由于单元测试时, UUT 的依赖全部都被 mock 掉了. 一定要配合集成测试和功能测试
  来保证模块之间的协作是通畅的. 否则可能会导致 API 输入或输出与实际不符的 bug.

- UTs might not catch unexpected bugs, because they are isolated out of UUT.

design patterns
^^^^^^^^^^^^^^^
- 单元测试只对 "变" 的东西进行测试, 不测试 "不变" 的东西. UT should test only
  logic, flows, configuration, etc. that changes, of a UUT.  Don't test
  constants, because it's useless -- constants nevers changes it's written as
  is and works as is.

  这里 constant 的含义是广泛的, 不仅仅是写死在代码中的常量, 还包含例如不变的
  模板文件等不会变的固定的 entity.

  在单元测试中, 需要仔细考虑什么是变的, 什么是不变的, 才能只对变化的部分做测试.

- 单元测试的组织形式.

  * 每个源代码模块对应一个单元测试文件.

  * 对每个 class 和 function, 至少有一个 unit test, 即使只是 placeholder test.
    (See `questions and concerns`_ for reason.)

  * Every function/method should have at least one test case.

  * Any ``if`` statement means an extra test.

  * Any ``try/catch`` exception handling means an extra test.

- 一个单元测试用例应该尽可能地短. 它应该只测试一个行为, 并在执行测试之前进行最
  小程度的准备操作.

  * 每个测试用例只测试一个行为, 则对多个行为的检测是并行的, 在执行测试时可以同
    时对多个行为点进行检测.
  
  * An ideal unit test, when it fails, you don't need to dig into traceback,
    you can see what exact point is failing just by looking at the test case's
    name.  当然, 这种理想情况可能实际中很难达到, 但这是每个单元测试应该去努力的
    方向.

- UT 应当保证足够迅速, it must be fast. 独立的单元测试则可以尽可能地保证这一点.
  保证快速的 UT 的意义是
  
  * 所有的实现细节都是由 UT 来驱动开发的. UT 必须频繁执行, 所以只有快速, 才能保
    证一个快速的 fedback cycle, 从而维持一个灵活的 (敏捷的) 开发节奏. (注意
    Faster UT doesn't make a faster development, but an agile development.)

    In other words, 如果要实践 TDD 这个开发方法, UTs 必须要快. 这对 UT suites
    的设计、实现与优化等方面, 具有一定的要求.

  * If UTs are slow, you’ll start to avoid running your tests, which may lead
    to bugs getting through.

- 单元测试应该尽量保证独立性, 只测试 UUT 本身, 而不测试它的依赖. 一个独立的单元
  测试的成功和失败不依赖于任何外部依赖. 这需要使用 mock 来达成.

  有些时候, UUT 与它的依赖或者说它外部的东西的界限不是那么清晰的, 例如当使用
  framework 时. 这时, 不可避免地, unit test 变成了一定程度上的 integration
  test. 这没有绝对清晰的界限. 只能说, 能保证独立时尽量保证独立.

  如果要写保证具有完善的独立性的单元测试, 不可避免地需要接触和了解一定程度的
  implementation details, 以保证自己的代码之外的逻辑能及时切断. 这一点, 尤其是
  当自己的代码与 framework 交互时尤其显著. 此时, 我们需要了解一些 framework 本
  身的实现细节.

- 同一个行为点尽量避免在不同的单元测试中重复测试.
  
  * 区分清晰模块功能的归属关系才能避免单元测试的重复.

    例如, module A depends on module B. 作为一个整体, AB 面对 3 种输入有三种输
    出.  然而, 这三种情况实际上完全是由于 B 存在 3 种情况. 而 A 只是对 B 的输入
    输出进行预处理. 所以对 A 单独而言, 并不存在 3 种情况. 那么对 A 的单元测试只
    需测试预处理逻辑部分即可. 对 B 的单元测试则需要测试 3 种情况. 不该对 A 测试
    3 种情况, 再重复对 B 测试相同的三种情况.

  * 如果好几个测试用例都在测试相似的内容, 那么它们本身应该合并为一个测试用例.

- 清晰哪些是公有 API, 哪些是内部实现细节. 避免测试实现细节 (除非涉及依赖调用处
  需要 mock).
  
  * (错误地) 检测被测功能的实现而不是它的 API, 会导致多处重复.

  * 检测实现细节会让测试与实现强耦合, 提高代码重构成本.

- unit tests 校验程序模块对内的功能, 只要模块 API 不变, unit tests 的逻辑就应该
  不变.

- UTs 的设计应该能够为重构提供保障, 但又不会过度地干预实现细节, 从而变成重构的
  阻碍.

- 测试用例中, 对预期结果的构建, 要避免使用与 SUT 相同的算法进行构建. 这样就起不
  到测试结果是否相符的目的了. 此时, 应该在测试用例中直接给出预期的结果, 与 SUT
  生成的结果进行比较. 若 SUT 生成的结果受一些外部因素影响而无法由输入来完全确定,
  必须要通过 mock 等手段将影响因素剔除, 保证输出的稳定性, 可复现性.

why testing
-----------

correctness
^^^^^^^^^^^

- 自动化的 UT/IT/FT 等最大的价值是, 它们提供了低成本高效率可重复的 bug
  detection mechanism.

- 在研发一个功能时, 这个 bug detection system 有助于保证代码实现总是与预期是一
  致的.  这是一个正确性方面的保证. 也是开发者对程序信心的基础.
  
- 在研发新功能或重构原有功能时, 这个 bug detection system 对避免 regression 问
  题有很大价值.

clean, maintainable code
^^^^^^^^^^^^^^^^^^^^^^^^

- 由于 regression test 变得很容易, 所以开发者愿意放心地做代码重构, 不会有心理
  障碍. 他知道如果 refactor 出了问题, 测试集会告诉他.

- Since we can confidently refactor our application constantly, we’re never
  scared to try to improve its design, it's more likely to end up with clean,
  maintainable code.

- Trying to improve the speed of your test suite and try to make it more
  effective, will ultimately deliver a better code quality.

productive workflow
^^^^^^^^^^^^^^^^^^^

- Tests can give us feedback about our work really quickly.

- They help us iterate fastly.

joy
^^^

- 自动化的测试有助于提高程序员的对程序信心和编码过程的愉悦.

- They help take some of the stress out of development.


design patterns
===============
- Each test should only test one thing. Just like each function should only
  does one thing.

  * 对于功能测试, 一个 test case 只测试一个 user story. 注意到一个 user story 
    可能很长, 需要检测很多个功能点.

  * 对于单元测试, 一个 test case 只测试被测对象的一个行为点. 对一个行为点的
    检测, 应该只需要一个或少量几个相关的 assertions. 避免多个 assertions 串在
    一起.

  意义:
  
  * 模块化、重用、职责清晰
    
  * 由于每个测试是独立执行的, 每个测试只检测一个问题, 有助于同时检测和发现
    多个问题. 如果将多个不相互依赖的测试逻辑放在一个测试单元中执行, 第一个
    不通过的部分就会 raise exception, 后续的测试则不会执行.

  * It helps you isolate the exact problem you may have, when you later come
    and change your code and accidentally introduce a bug.

- Ensure isolations between test cases.

  * Properly isolated tests can be run in any sequence.

  * Always rebuild your starting state from scratch.

  * 如果多个测试需要共享某个初始状态, each test must cleans up properly after
    itself.

- Carefully deal with tested code containing asynchronous operation.

  * Best solution: 对于异步操作, 如果它接受传入 callback 是最好的. 此时可利用
    callback 去检测结果.

  * Normal solution: Polling the result of async operation. Caller 必须等着
    结果返回, 让异步变成同步. 不能让异步操作就那么溜过去. 设置尽量小的 polling
    frequency, 并设置 polling upper bound. (Avoid hardcode single sleep.)

- Ensure tests are deterministic.
  
  A test is non-deterministic when it passes sometimes and fails sometimes,
  without any noticeable change in the code, tests, or environment. Such tests
  fail, then you re-run them and they pass.

  Non-deterministic tests have two problems:

  * They are useless.

  * They infects the whole test suite. Initially people will look at the
    failure report and notice that the failures are in non-deterministic tests,
    but soon they'll lose the discipline to do that. Once that discipline is
    lost, then a failure in the healthy deterministic tests will get ignored
    too. At that point you've lost the whole game and might as well get rid of
    all the tests.

  Analysis to non-deterministic tests:

  * 不确定性的测试的可能原因: 1) 测试之间没有保证更好的独立性; 2) 异步操作
    在时间上的不确定性导致测试结果不确定; 3) 测试需依赖于外部服务, 后者的
    不确定性 (例如可用性) 导致结果不确定.

  * 如果目前没有时间处理这些不确定性的测试, 先隔离至另一个 test suite. 然后
    及时处理. A danger here is that tests keep getting thrown into quarantine
    and forgotten, which means your bug detection system is eroding.

- Do not test for developer's stupidity. You should trust yourself (and fellow
  developers) not to do something deliberately stupid, but not something
  accidentally stupid. (If not, you have a much bigger problem.)

- Do not test for code performance or timing.

- 保证你的测试代码确实测试到了你想要测试的点. 即要保证测试代码本身的正确性. 否
  则测试没通过都不知道.

- Readability vs DRY for tests.[SODupUT]_

  * 对测试, 易读性是更重要的特性. If a test fails, you want the problem to be
    obvious.

  * 适当地 refactor 和抽象有助于保持测试的清晰可读, as long as it doesn't
    obscure anything, and eliminating the duplication in your tests may lead to
    a better API. 但太多抽象和 DRY 会损害测试结果的易读性. Developer shouldn't
    have to wade through a lot of heavily factored test code to determine
    exactly what failed.

- About engineering the test.

  * You should engineer the tests to make it run faster and more effectively.

  * You can build tools to achieve the above goals.

  * You can refactor and encapsulate your tests to make it more DRY, as long
    as its readability is not compromised.

  * Your testing code should be as respectable as your main code. Do it out of
    a sense of duty and professionalism.

- fake data.

  * 测试时可以使用比较符合实际的 fake data.

  * 保证测试数据的可重复性. 如果使用随机数据, 应保证每次独立执行的测试, 都使用
    相同的 seed. 或者, 对于每个 test case, 都应重置 seed. 这样, 测试用例之间
    对 PRNG 状态才没有依赖.

  * 类似地, 其他假数据相关的一些全局状态, 例如 global counter 等, 也可以在
    每个 test case 的初始化中进行重置.

- 测试用例的名字不怕长, 就怕不知道测的功能点是什么. 所以只要把测试点写清楚就好.

- Rule of thumb for different type of tests in a project (for an Ports/adapters
  architecture project).

  * unit test. 70%.

  * integration test. 20%.

  * UI test (functional test). 10%.

  Your architecture to some extent dictates the types of tests that you need.
  The more you can separate your business logic from your external
  dependencies, and the more modular your code, the closer you’ll get to a nice
  balance between unit tests, integration tests and functional tests.

  Identify the boundaries of your system—the points at which your code
  interacts with external systems, like the database or the filesystem, or the
  internet, or the UI—and trying to keep them separate from the core business
  logic of your application.

- Rescuing legacy code with tests.

  * 不要一上来就根据原始代码实现写一堆单元测试, 因为这样实际上固化了 legacy
    code 本身的模样. 这样不会让代码更好, 反而让后续的重构等优化更费力 (因为还需
    修改相应的 UTs).

  * 从宏观功能角度入手, 使用 FTs, ITs 等先将宏观的确定是预期的行为固定下来. 然后
    再慢慢细化, 对各个模块进行重构, 并用 TDD 或单纯的 UT 去优化和固定其行为.
    注意重点是不要固化原有的可能糟糕的实现, 而是固化经过思考、重构的实现.

Techniques
==========

test double
-----------
- Conventionally, mocks may refer collectively to test stub, test spy, and mock
  object.

mock
^^^^
- Mock 的基本概念是使用一个假的 service call 来替代真实的 service call, so that
  to eliminate dependencies. service call 本身的设计应该是一个不透明的接口, 即
  有规范设计的输入和输出. mock 能够完全替换这个 service call, 则需要具有完全 相
  同的接口.

  Mock 必须具有与原操作相同的接口, 才能发挥测试的意义. 即保证功能实现中对外部
  服务的调用是正确的.

- 必要时还需要在单元测试中检查对 service call 的调用输入和输出的检测. 以保证对
  服务的调用确实是符合预期的 (因为 mock 接口正确还不够, 调用参数还需要正确.)

- The usage of mocks.

  * to eliminate dependencies for a UUT.

  * When a dependency has no return value. (behavior verification)

  * Ease the testing of different UUT logic branches. 有时候一些逻辑分支很难
    在真实情况下构建, 使用 mock 则可以轻易地伪造实际中难以测试的情况.

  * eliminate dependency on database calls, to speed up unit testing.

  * Don't have to wait for implementing UUT's dependency to test the UUT.
    (Outside-In TDD)

- Listen to your tests. If a "dependency is hard to mock, then it's
  definitely hard to use for the object that'll actually be using it."

  换句话说, 如果在测试代码中发现被测功能的某个依赖 mock 起来比较费劲,
  那说明它的 API 不太容易使用, 可能需要重构这个依赖的 API.

  例如, 以下 API 根据一个文件的内容生成一些数据.

  .. code:: python

    @classmethod
    def all_swaps(cls):
        entries = []
        with open(cls.swapinfo, "r") as f:
            # skip first line
            f.readline()
            for line in f:
                name, type, size, used, priority = line.strip().split()
                entries.append(cls.swap_entry_class(
                    name, type, int(size), int(used), int(priority)
                ))
        return entries

  假如我们现在不想引入 fixture 文件, 为保证测试独立性, 需要 mock 掉调用 IO 的过
  程. 但 builtin ``open()`` 没那么容易 mock. 这就要求我们封装一个 IO 调用的抽象
  函数, 与数据生成逻辑隔离开来.

  .. code:: python

    @classmethod
    def all_swaps(cls):
        entries = []
        for line in cls.iter_swaps():
            name, type, size, used, priority = line.strip().split()
            entries.append(cls.swap_entry_class(
                name, type, int(size), int(used), int(priority)
            ))
        return entries

    @classmethod
    def iter_swaps(cls):
        with open(cls.swapinfo, "r") as f:
            # skip first line
            f.readline()
            yield from f

  事实上整个代码结构还更清晰了.

- 如果一个测试用例需要很多 mock 才能保证被测功能与它的依赖隔离开来, 才能
  保证仅仅是在测试该层的功能逻辑, 则说明代码实现可能可以优化, 降低耦合.

- 在一个功能的单元测试中, 对 mock 调用情况的检测不可避免地是在测试功能的实现细节,
  而不是它的 API. 因此, 过分地对 mock 的测试可能导致测试用例与功能实现细节强耦合.
  If you’re not careful, this can start to work against one of the supposed
  benefits of having tests, which was to encourage refactoring. You can find
  yourself having to change dozens of mocky tests and contract tests when you
  want to change an internal API.

  而另一方面, 对 mock 调用的检验却也是必不可少的. 因为我们在单元测试时, 人为地将
  外部服务从功能代码中切断, 硬生生地切出来第三组 (输入输出之外) 接口. 少了真实
  的外部服务对代码逻辑的检验, 就要求我们去检验代码对这组接口的访问情况, 以保证
  正确性.

  此外, 在 Outside-In TDD 中, mock 是保证单元测试隔离性的必要手段. 即需要 mock
  掉所有它依赖的 (从而是尚未实现的) 模块 API.

  因此, 构造对 mock 的检验需要谨慎小心. 尽量一般化, 考虑到多种可能的调用模式,
  避免被测功能逻辑没有修改, 却需要测试代码跟着 external service 调用的修改而
  修改的问题.

  It’s better to test behaviour, not implementation details; test what happens,
  not how you do it. Mocks often end up erring too much on the side of the
  "how" rather than the "what".

- 在 dynamic language 中, 经常使用 monkey patching 方法来 dynamically
  substitute calls to external services with a mock.

- 以 python 为例, 手动 mock 与单元测试的流程大致为:

  .. code:: python

    def test_foo():

        def fake_call(arg1, arg2, kwarg1=foo, kwarg2=bar):
            fake_call.arg1 = arg1
            fake_call.arg2 = arg2
            fake_call.kwarg1 = kwarg1
            fake_call.kwarg2 = kwarg2
            return value

        # mock
        module.external_call = fake_call
        # call operation being tested
        ret = operation_being_tested(a, b, c)
        # test operation's result and side effects
        # ...
        # test service call
        assert fake_call.arg1 == "something"
        assert fake_call.arg2 == "something else"

- 很多语言已经提供方便的 mock library, 一般无需手动构建替代的 mock function, 也
  无需手动替换方法和调用.

- 在 mock 时, 一定要正确地判断 UUT 依赖的 API 是什么, 即它与外部交互的点是什么.
  只应该 mock dependency 与 UUT 交互处的 API. 而不该去 mock 更多的东西. 例如,
  UUT 调用另一个模块中的 ``cls.method``, 只应该 mock ``cls.method``, 而不该去
  mock ``cls`` 整体.

test fixtures
-------------
- A test fixture is a fixed state of a set of objects used as a baseline for
  running tests.

- The purpose of a test fixture is to ensure that there is a well known and
  fixed environment in which tests are run.

- Fixture can be not only database states, but also general precondition setup
  in all other aspects.

- The benefits of test fixture.

  * reduce duplication and automatically ensure baseline environment is always
    set up before running a test.

references
==========
.. [SODupUT] `Is duplicated code more tolerable in unit tests? <https://stackoverflow.com/questions/129693/is-duplicated-code-more-tolerable-in-unit-tests>`_
.. [SOITExAPI] `How are integration tests written for interacting with external API? <https://stackoverflow.com/questions/7564038/how-are-integration-tests-written-for-interacting-with-external-api>`_
.. [SETDDOutsideInInsideOut] `TDD - Outside In vs Inside Out <https://softwareengineering.stackexchange.com/questions/166409/tdd-outside-in-vs-inside-out>`_
