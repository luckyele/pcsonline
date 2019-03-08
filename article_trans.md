
**《Python思想：设计模式和问题解决技巧》**

---

# 第二章  单元测试

我最近有一个重要的体会，即单元测试具有巨大价值。

所谓单元测试，就是在全部代码中进行集成测试的过程。每次生成程序时都会进行这些测试。这种方法实际上是对编译器作了一定程度的扩展, 告诉编译器很多程序应该做的事情。构建出来的程序不仅会检查语法错误，而且也会检查语义错误。

类似C的语言, 特别是C++，通常在性能方面的价值高于编程安全。Java 开发程序速度比 c++ 快得多 (大约快两倍，对多数用户) ，这是因为Java 具有更多的安全机制，如更好的类型检查、强制异常和垃圾回收等。通过将单元测试集成到生成过程中, 可以扩展安全网络, 可以加快开发。开发人员在修改程序时可以更大胆一些，易于通过重构代码以发现设计或实现缺陷，通常也会更快地生成更好的产品。

>Unit testing is not generally considered a design pattern; in fact, it might be considered a “development pattern,” but perhaps there are enough “pattern” phrases in the world already. Its effect on development is so significant that it will be used throughout this book, and thus will be introduced here.

单元测试通常并不会被认为是一种设计模式；事实上, 它可能被认为是一种 开发模式，对开发具有重要影响。单元测试将贯穿全书， 因此这里先介绍一下。

我的单元测试经验开始于自己意识到一本书的每个程序必须自动提取和组织到一个源码树中。连同适当的 makefiles（或其它等价技术）, 你只需键入make，即可以创建整个树。这个过程，对本书代码质量的影响是直接的、巨大的，很快成为所有编程书籍的必备品。你怎么能相信自己没有编译的代码呢？我还发现，如果想进行彻底改变, 可以在整本书中使用搜索和替换。我知道如果引入一个缺陷, 代码抽取器和makefiles 会把它刷新出来。

>As programs became more complex, however, I also found that there was a serious hole in my system. Being able to successfully compile programs is clearly an important first step, and for a published book it seemed a fairly revolutionary one—usually due to the pressures of publishing, it's quite typical to randomly open a programming book and discover a coding flaw. However, I kept getting messages from readers reporting semantic problems in my code (in Thinking in Java). These problems could only be discovered by running the code. Naturally, I understood this and had taken some early faltering steps towards implementing a system that would perform automatic execution tests, but I had succumbed to the pressures of publishing, all the while knowing that there was definitely something wrong with my process and that it would come back to bite me in the form of embarrassing bug reports (in the open source world, embarrassment is one of the prime motivating factors towards increasing the quality of one’s code!).

然而, 随着程序变得更加复杂, 我发现系统有一个严重漏洞。能成功编译程序是重要的第一步，对一本出版物来说具有革命性。由于出版压力, 通常是随机打开一本编程书，以发现编码缺陷。但是, 我不断从读者那里得到反馈代码语义问题 (在 Java 中的思维)。这些问题只能通过运行代码来发现。当然, 我理解这一点并采取了一些步骤实现一个能自动执行测试的系统。但我也屈服于出版的压力，知道肯定有错的东西伴随程序，它会以尴尬的 bug 报告 的形式回来咬我(在开源世界, 尴尬是一个主要激励因素, 以提高代码质量!)。

>The other problem was that I was lacking a structure for the testing system. Eventually, I started hearing about unit testing and JUnit11, which provided a basis for a testing structure. However, even though JUnit is intended to make the creation of test code easy, I wanted to see if I could make it even easier, applying the Extreme Programming principle of “do the simplest thing that could possibly work” as a starting point, and then evolving the system as usage demands (In addition, I wanted to try to reduce the amount of test code, in an attempt to fit more functionality in less code for screen presentations). This chapter is the result. 

另一个问题是, 我缺乏一个测试系统的结构。最后, 我开始听到单元测试和 JUnit11, 这为测试结构提供了基础。然而, 尽管 JUnit 旨在使创建测试代码变容易, 但我想看看是否能变得更容易, 应用 "做最简单的事情, 可能工作" 作为出发点, 然后演变系统的极端编程原理由于使用要求 (此外, 我想尝试减少测试代码的数量, 试图在更少的代码中为屏幕演示提供更多的功能)。本章是结果。

>## Write tests first
As I mentioned, one of the problems that I encountered—that most people encounter, it turns out—was submitting to the pressures of publishing and as a result letting tests fall by the wayside. This is easy to do if you forge ahead and write your program code because there’s a little voice that tells you that, after all, you’ve got it working now, and wouldn’t it be  more interesting/useful/expedient to just go on and write that other part (we can always go back and write the tests later). As a result, the tests take on less importance, as they often do in a development project.

## 先写测试
正如我所提到的, 我遇到的一个问题--大多数人所遇到的, 事实证明--屈服于出版的压力, 结果是忽略了测试。这是很容易做到的, 如果你开拓进取, 写你的程序代码, 因为有一个小的声音告诉你, 毕竟, 你现在已经得到了它的工作, 是不是更有趣/有用/权宜之计只是去写的其他部分 (我们可以总是回去和 稍后编写测试)。因此, 测试的重要性不那么重要, 因为它们通常在开发项目中进行。

>The answer to this problem, which I first found described in Extreme Programming Explained, is to write the tests before you write the code.

这个问题的答案, 我第一次在极限编程中所描述的, 是在编写代码之前编写测试。

>This may seem to artificially force testing to the forefront of the development process, but what it actually does is to give testing enough additional value to make it essential. If you write the tests first, you:
 
这似乎是人为地强制测试到开发过程的最前沿, 但它实际上做的是给测试足够的附加价值, 使其必不可少。如果首先编写测试, 则:
 
>1. Describe what the code is supposed to do, not with some external graphical tool but with code that actually lays the specification down in concrete, verifiable terms.
2. Provide an example of how the code should be used; again, this is a working, tested example, normally showing all the important method calls, rather than just an academic description of a library. 
3. Provide a way to verify when the code is finished (when all the tests run correctly).

1. 描述代码应该做什么, 而不是用一些外部图形工具, 而是用具体的、可验证的术语实际地将规范下的代码。

2. 提供如何使用守则的例子;同样, 这是一个工作的、经过测试的示例, 通常显示所有重要的方法调用, 而不仅仅是一个库的学术描述。

3. 提供一种方法来验证代码何时完成 (当所有测试都正确运行时)。

>Thus, if you write the tests first then testing becomes a development tool, not just a verification step that can be skipped if you happen to feel comfortable about the code that you just wrote (a comfort, I have found, that is usually wrong).

因此, 如果您首先编写测试, 则测试将成为开发工具, 而不仅仅是一个验证步骤, 如果您碰巧对您刚刚编写的代码感到满意 (我发现, 这通常是错误的), 就可以跳过它。

>You can find convincing arguments in Extreme Programming Explained, as “write tests first” is a fundamental principle of XP. If you aren’t convinced you need to adopt any of the changes suggested by XP, note that according to Software Engineering Institute (SEI) studies, nearly 70% of software organizations are stuck in the first two levels of SEI's scale of sophistication: chaos, and slightly better than chaos. If you change nothing else, add automated testing. Simple Python testing Sanity check for a quick test of the programs in this book, and to append the output of each program (as a string) to its listing:

你可以在极限编程中找到令人信服的论据, 因为 "先写测试" 是 XP 的基本原则。如果您不确信您需要采用 XP 建议的任何更改, 请注意, 根据软件工程研究所 (sei) 的研究, 近70% 的软件组织被困在 SEI 的复杂程度的前两个层次: 混乱和稍微好过混乱。如果您不更改其他任何东西, 请添加自动测试。简单 Python 测试的正确性检查本书中的程序的快速测试, 并将每个程序 (作为字符串) 的输出追加到其列表中:


    #: SanityCheck.py
    import string, glob, os
    # Do not include the following in the automatic
    # tests:
    exclude = ("SanityCheck.py", "BoxObserver.py",)
    def visitor(arg, dirname, names):
    dir = os.getcwd()
    os.chdir(dirname)
    try:
    	pyprogs = [p for p in glob.glob('*.py')
    if p not in exclude ]
    if not pyprogs: return
    print '[' + os.getcwd() + ']'
    for program in pyprogs:
    print '\t', program
    os.system("python %s > tmp" % program)
    file = open(program).read()
    output = open('tmp').read()
    # Append output if it's not already there:
    if file.find("output = '''") == -1 and \
    len(output) > 0:
    divider = '#' * 50 + '\n'
    file = file.replace('#' + ':~', '#<hr>\n')
    file += "output = '''\n" + \
    open('tmp').read() + "'''\n"
    open(program,'w').write(file)
    finally:
    os.chdir(dir)
    if __name__ == "__main__":
    os.path.walk('.', visitor, None)
    #:~


>Just run this from the root directory of the code listings for the book; it will descend into each subdirectory and run the program there. An easy way to check things is to redirect standard output to a file, then if there are any errors they will be the only thing that appears at the console during program execution.

只需从该书的代码清单的根目录中运行此项即可。它将下降到每个子目录, 并运行该程序在那里。检查事情的一个简单方法是将标准输出重定向到一个文件, 然后如果有任何错误, 它们将是在程序执行过程中唯一出现在控制台上的东西。

> A very simple framework

## 一个非常简单的框架

>As mentioned, a primary goal of this code is to make the writing of unit testing code very simple, even simpler than with JUnit. As further needs are discovered during the use of this system, then that functionality can be added, but to start with the framework will just provide a way to easily create and run tests, and report failure if something breaks (success will produce no results other than normal output that may occur during the running of the test). 

如上所述, 此代码的主要目标是使单元测试代码的编写非常简单, 甚至比使用 JUnit 简单。由于在使用此系统时发现了进一步的需求, 因此可以添加该功能, 但从框架开始只需提供一种轻松创建和运行测试的方法, 如果有什么中断则报告失败 (成功将不会产生任何结果, 而不是在测试运行期间可能发生的正常输出)。

>My intended use of this framework is in makefiles, and make aborts if there is a non-zero return value from the execution of a command. The build process will consist of compilation of the programs and execution of unit tests, and if make gets all the way through successfully then the system will be validated, otherwise it will abort at the place of failure. The error messages will report the test that failed but not much else, so that you can provide whatever granularity that you need by writing as many tests as you want, each one covering as much or as little as you find necessary.

我对此框架的预期用途是在 makefiles 中, 如果命令的执行中有一个非零的返回值, 则将中止。生成过程将包括程序的编译和单元测试的执行, 如果使通过成功获得所有方法, 则系统将被验证, 否则它将在失败的地方中止。错误消息将报告失败的测试, 但没有太多其他内容, 因此您可以根据需要编写尽可能多的测试来提供所需的任何粒度, 每一个都覆盖尽可能少的内容。

>In some sense, this framework provides an alternative place for all those “print” statements I’ve written and later erased over the years. 

从某种意义上说, 这个框架为我写的所有这些 "打印" 语句提供了一个替代的位置, 并在以后的几年中被删除。

>To create a set of tests, you start by making a static inner class inside the class you wish to test (your test code may also test other classes; it’s up to you). This test code is distinguished by inheriting from UnitTest: 

要创建一组测试, 首先要在要测试的类内建立一个静态内部类 (测试代码也可以测试其他类; 这由你自己来做)。此测试代码通过从单元测试继承来区分:



    # test:UnitTest.py
    # The basic unit testing class
    class UnitTest:
    static String testID
    static List errors = ArrayList()
    # Override cleanup() if test object
    # creation allocates non-memory
    # resources that must be cleaned up:
    def cleanup(self):
    # Verify the truth of a condition:
    protected final void affirm(boolean condition){
    if(!condition)
    errors.add("failed: " + testID)
    # :~

>The only testing method [[ So far ]] is affirm( )12, which is protected so that it can be used from the inheriting class. All this method does is verify that something is true. If not, it adds an error to the list,  reporting that the current test (established by the static testID, which is set by the testrunning program that you shall see shortly) has failed. Although this is not a lot of information—you might also wish to have the line number, which could be extracted from an exception—it may be enough for most situations.

唯一的测试方法 [[到目前]] 是肯定的 () 12, 它是受保护的, 以便它可以从继承类使用。所有此方法都是验证某事是否属实。如果不是, 它会向列表中添加错误, 报告当前测试 (由由您稍后将看到的 testrunning 程序设置的静态 testID 所建立) 失败。虽然这不是很多信息-你可能也希望有行号, 这可以从一个例外提取-它可能是足够的大多数情况下。

>Unlike JUnit (which uses setUp( ) and tearDown( ) methods), test objects will be built using ordinary Python construction. You define the test objects by creating them as ordinary class members of the test class, and a new test class object will be created for each test method (thus preventing any problems that might occur from side effects between tests). Occasionally, the creation of a test object will allocate non-memory resources, in which case you must override cleanup( ) to release those resources.

与 JUnit (使用安装程序 () 和拆卸 () 方法) 不同, 测试对象将使用普通的 Python 构造生成。通过将测试对象创建为测试类的普通类成员, 并为每个测试方法创建一个新的测试类对象 (从而防止在测试之间的副作用可能发生的任何问题), 您可以定义它们。有时, 创建测试对象将分配非内存资源, 在这种情况下, 必须重写清除 () 以释放这些资源。

>## Writing tests

>Writing tests becomes very simple. Here’s an example that creates the necessary static inner class and performs trivial tests:

## 编写测试

编写测试变得非常简单。下面是一个创建必要的静态内部类并执行琐碎测试的示例:

    # c02:TestDemo.py
    # Creating a test
    12 I had originally called this assert(), but that word became reserved in JDK 1.4
    when assertions were added to the language.
    class TestDemo:
    private static int objCounter = 0
    private int id = ++objCounter
    public TestDemo(String s):
    print (s + ": count = " + id)
    def close(self):
    print ("Cleaning up: " + id)
    def someCondition(self): return 1
    public static class Test(UnitTest):
    TestDemo test1 = TestDemo("test1")
    TestDemo test2 = TestDemo("test2")
    def cleanup(self):
    test2.close()
    test1.close()
    def testA(self):
    print “TestDemo.testA“
    affirm(test1.someCondition())
    def testB(self):
    print “TestDemo.testB“
    affirm(test2.someCondition())
    affirm(TestDemo.objCounter != 0)
    # Causes the build to halt:
    #! public void test3(): affirm(0)
    # :~

>The test3( ) method is commented out because, as you’ll see, it causes the automatic build of this book’s source-code tree to stop. You can name your inner class anything you’d like; the only important factor is that it extends UnitTest. You can also include any necessary support code in other methods. Only public methods that take no arguments and return void will be treated as tests (the names of these methods are also not constrained).

test3 () 方法被注释掉, 因为正如您所看到的那样, 它会导致此书的源代码树的自动生成停止。你可以命名你的内在类任何你想要的东西;唯一重要的因素是它扩展了单元测试。还可以在其他方法中包括任何必要的支持代码。只有不带参数和返回 void 的公共方法才会被视为测试 (这些方法的名称也不受约束)。

>The above test class creates two instances of TestDemo. The TestDemo constructor prints something, so that we can see it being called. You could also define a default constructor (the only kind that is used by the test framework), although none is necessary here. The TestDemo class has a close( ) method which suggests it is used as part of object cleanup, so this is called in the overridden cleanup( ) method in Test.

上面的测试类创建两个 TestDemo 实例。TestDemo 构造函数打印一些东西, 这样我们就可以看到它被调用。您还可以定义默认构造函数 (测试框架使用的唯一类型), 尽管此处没有必要。TestDemo 类有一个 close () 方法, 它建议将它用作对象清理的一部分, 因此在测试中的重写清理 () 方法中调用这一点。

>The testing methods use the affirm( ) method to validate expressions, and if there is a failure the information is stored and printed after all the tests are run. Of course, the affirm( ) arguments are usually more complicated than this; you’ll see more examples throughout the rest of this book.

测试方法使用确认 () 方法验证表达式, 如果出现故障, 则在所有测试运行后存储和打印信息。当然, 肯定 () 论据通常比这更复杂;在本书的其余部分中, 您将看到更多的示例。

>Notice that in testB( ), the private field objCounter is accessible to the testing code—this is because Test has the permissions of an inner class.

请注意, 在 testB () 中, 测试代码可以访问私有字段 objCounter, 这是因为测试具有内部类的权限。

>You can see that writing test code requires very little extra effort, and no knowledge other than that used for writing ordinary classes. 

您可以看到, 编写测试代码需要很少的额外的努力, 而且除了用于编写普通类的知识外, 没有其他信息。

>To run the tests, you use RunUnitTests.py (which will be introduced shortly). The command for the above code looks like this:

要运行测试, 请使用 RunUnitTests (不久将介绍)。上面代码的命令如下所示:

```java com.bruceeckel.test.RunUnitTests TestDemo```

>It produces the following output:

它产生如下输出：

    test1: count = 1
    test2: count = 2
    TestDemo.testA
    Cleaning up: 2
    Cleaning up: 1
    test1: count = 3
    test2: count = 4
    TestDemo.testB
    Cleaning up: 4
    Cleaning up: 3

>All the output is noise as far as the success or failure of the unit testing is concerned. Only if one or more of the unit tests fail does the program returns a non-zero value to terminate the make process after the error messages are produced. Thus, you can choose to produce output or not, as it suits your needs, and the test class becomes a good place to put any printing code you might need—if you do this, you tend to keep such code around rather than putting it in and stripping it out as is typically done with tracing code.

>If you need to add a test to a class derived from one that already has a test class, it’s no problem, as you can see here:

在单元测试的成败方面, 所有输出都是噪音。只有当一个或多个单元测试失败时, 程序才返回非零值, 以便在生成错误消息后终止生成过程。因此, 您可以选择生成输出或者不输出, 因为它适合您的需要, 并且测试类将成为放置任何可能需要的打印代码的好地方-如果您这样做, 您倾向于保留此类代码, 而不是将其放入并剥离出来, 通常是用 t 来完成的。赛车密码

如果需要向从已经有测试类的类中添加测试, 则没有问题, 您可以在这里看到:

    # c02:TestDemo2.py
    # Inheriting from a class that
    # already has a test is no problem.
    class TestDemo2(TestDemo):
    public TestDemo2(String s): .__init__(s)
    # You can even use the same name
    # as the test class in the base class:
    public static class Test(UnitTest):
    def testA(self):
    print “TestDemo2.testA“
    affirm(1 + 1 == 2)
    def testB(self):
    print “TestDemo2.testB“
    affirm(2 * 2 == 4)
    # :~

>Even the name of the inner class can be the same. In the above code, all the assertions are always true so the tests will never fail.

>即使内部类的名称也可以相同。在上面的代码中, 所有断言都是正确的, 因此测试不会失败。

>## White-box & black-box tests
## 白箱和黑箱测试

>The unit test examples so far are what are traditionally called white-box tests. This means that the test code has complete access to the internals of the class that’s being tested (so it might be more appropriately called “transparent box” testing). White-box testing happens automatically when you make the unit test class as an inner class of the class being tested, since inner classes automatically have access to all their outer class elements, even those that are private.

到目前为止, 单元测试示例是传统上称为白盒测试的。这意味着测试代码对正在测试的类的内部进行了完全访问 (因此它可能更恰当地称为 "透明框" 测试)。当将单元测试类作为正在测试的类的内部类时, 白盒测试会自动发生, 因为内部类可以自动访问其所有外部类元素, 即使是私有的。

>A possibly more common form of testing is black-box testing, which refers to treating the class under test as an impenetrable box. You can’t see the internals; you can only access the public portions of the class. Thus, black-box testing corresponds more closely to functional testing, to verify the methods that the client programmer is going to use. In addition, black-box testing provides a minimal instruction sheet to the client programmer – in the absence of all other documentation, the black-box tests at least demonstrate how to make basic calls to the public class methods.

一个可能更常见的测试形式是黑盒测试, 它指的是将测试中的类作为一个坚不可摧的盒子来处理。你看不到内部;只能访问类的公共部分。因此, 黑盒测试更接近于功能测试, 以验证客户端程序员将要使用的方法。此外, 黑盒测试为客户端程序员提供了最小的指令表-在没有所有其他文档的情况下, 黑箱测试至少演示了如何对公共类方法进行基本调用。

>To perform black-box tests using the unit-testing framework presented in this book, all you need to do is create your test class as a global class instead of an inner class. All the other rules are the same (for example, the unit test class must be public, and derived from 
UnitTest). 

要使用本书中介绍的单元测试框架执行黑盒测试, 您需要做的就是将测试类创建为全局类而不是内部类。所有其他规则都是相同的 (例如, 单元测试类必须是公共的, 并从单元测试派生)。

>There’s one other caveat, which will also provide a little review of Java packages. If you want to be completely rigorous, you must put your blackbox test class in a separate directory than the class it tests, otherwise it will have package access to the elements of the class being tested. That is, you’ll be able to access protected and friendly elements of the class being tested. Here’s an example:

还有一个警告, 它还将提供对 Java 包的一点评论。如果要完全严格, 必须将黑箱测试类放在单独的目录中, 而不是它测试的类, 否则它将对正在测试的类的元素具有包访问权限。即, 您将能够访问被测试的类的受保护和友好的元素。下面是一个示例:

    # c02:Testable.py
    class Testable:
    private void f1():
    def f2(self): # "Friendly": package access
    def f3(self): # Also package access
    def f4(self):
    # :~

>Normally, the only method that should be directly accessible to the client programmer is f4( ). However, if you put your black-box test in the same directory, it automatically becomes part of the same package (in this case, the default package since none is specified) and then has inappropriate access:

通常, 客户端程序员应该直接访问的唯一方法是 f4 ()。但是, 如果将黑盒测试放在同一个目录中, 它将自动成为同一个包的一部分 (在这种情况下, 默认的程序包是从没有指定的), 然后有不适当的访问权限:

    # c02:TooMuchAccess.py
    class TooMuchAccess(UnitTest):
    Testable tst = Testable()
    def test1(self):
    tst.f2() # Oops!
    tst.f3() # Oops!
    tst.f4() # OK
    # :~


>You can solve the problem by moving TooMuchAccess.py into its own subdirectory, thereby putting it in its own default package (thus a different package from Testable.py). Of course, when you do this, then Testable must be in its own package, so that it can be imported (note that it is also possible to import a “package-less” class by giving the class name in the import statement and ensuring that the class is in your CLASSPATH):

您可以通过将 TooMuchAccess.py到其自己的子目录来解决此问题, 从而将其放在其自己的默认包中 (由此可测试的不同包)。当然, 当您这样做时, 可测试必须在其自己的包中, 以便可以导入 (请注意, 通过在 import 语句中提供类名并确保该类位于类路径中, 也可以导入 "package-less "类):

    # c02:testable:Testable.py
    package c02.testable
    class Testable:
    private void f1():
    def f2(self): # "Friendly": package access
    def f3(self): # Also package access
    def f4(self):
    # :~

>Here’s the black-box test in its own package, showing how only public methods may be called:

下面是它自己的包中的黑箱测试, 显示了如何只调用公共方法:
    
    # c02:test:BlackBoxTest.py
    class BlackBoxTest(UnitTest):
    Testable tst = Testable()
    def test1(self):
    #! tst.f2() # Nope!
    #! tst.f3() # Nope!
    tst.f4() # Only public methods available
    # :~

>Note that the above program is indeed very similar to the one that the client programmer would write to use your class, including the imports and available methods. So it does make a good rogramming example. Of course, it’s easier from a coding standpoint to just make an inner class, and unless you’re ardent about the need for specific black-box testing you may just want to go ahead and use the inner classes (with the knowledge that if you need to you can later extract the inner classes into separate black-box test classes, without too much effort). 

请注意, 上述程序确实非常类似于客户端程序员使用您的类编写的, 包括导入和可用方法。所以它确实是一个很好的 rogramming 例子。当然, 从编码的角度来说, 仅仅制作一个内部类比较容易, 除非你热衷于特定的黑盒测试, 否则你可能只是想继续使用内部类 (如果你需要的话, 你可以在以后提取内部 类分为单独的黑箱测试类, 不需要太多的努力)。

>Running tests The program that runs the tests makes significant use of reflection so that writing the tests can be simple for the client programmer.

运行测试的程序可以很好地使用反射, 以便编写测试对客户端程序员来说很简单。

    # test:RunUnitTests.py
    # Discovering the unit test
    # class and running each test.
    class RunUnitTests:
    public static void
    require(boolean requirement, String errmsg):
    if(!requirement):
    System.err.println(errmsg)
    System.exit(1)
    def main(self, String[] args):
    require(args.length == 1,
    "Usage: RunUnitTests qualified-class")
    try:
    Class c = Class.forName(args[0])
    # Only finds the inner classes
    # declared in the current class:
    Class[] classes = c.getDeclaredClasses()
    Class ut = null
    for(int j = 0 j < classes.length j++):
    # Skip inner classes that are
    # not derived from UnitTest:
    if(!UnitTest.class.
    isAssignableFrom(classes[j]))
    continue
    ut = classes[j]
    break # Finds the first test class only
    # If it found an inner class,
    # that class must be static:
    if(ut != null)
    require(
    Modifier.isStatic(ut.getModifiers()),
    "inner UnitTest class must be static")
    # If it couldn't find the inner class,
    # maybe it's a regular class (for black-
    # box testing:
    if(ut == null)
    if(UnitTest.class.isAssignableFrom(c))
    ut = c
    require(ut != null,
    "No UnitTest class found")
    require(
    Modifier.isPublic(ut.getModifiers()),
    "UnitTest class must be public")
    Method[] methods = ut.getDeclaredMethods()
    for(int k = 0 k < methods.length k++):
    Method m = methods[k]
    # Ignore overridden UnitTest methods:
    if(m.getName().equals("cleanup"))
    continue
    # Only public methods with no
    # arguments and void return
    # types will be used as test code:
    if(m.getParameterTypes().length == 0 &&
    m.getReturnType() == void.class &&
    Modifier.isPublic(m.getModifiers())):
    # The name of the test is
    # used in error messages:
    UnitTest.testID = m.getName()
    # A instance of the
    # test object is created and
    # cleaned up for each test:
    Object test = ut.newInstance()
    m.invoke(test, Object[0])
    ((UnitTest)test).cleanup()
    catch(Exception e):
    e.printStackTrace(System.err)
    # Any exception will return a nonzero
    # value to the console, so that
    # 'make' will abort:
    System.err.println("Aborting make")
    System.exit(1)
    # After all tests in this class are run,
    # display any results. If there were errors,
    # abort 'make' by returning a nonzero value.
    if(UnitTest.errors.size() != 0):
    Iterator it = UnitTest.errors.iterator()
    while(it.hasNext())
    System.err.println(it.next())
    System.exit(1)
    # :~

>## Automatically executing tests Exercises

>1. Install this book’s source code tree and ensure that you have a make utility installed on your system (Gnu make is freely available on the internet at various locations). In TestDemo.py, un-comment test3( ), then type make and observe the results.
2. Modify TestDemo.java by adding a new test that throws an exception. Type make and observe the results.
3. Modify your solutions to the exercises in Chapter 1 by adding unit tests. Write makefiles that incorporate the unit tests. 

##自动执行测试练习
1. 安装本书的源代码树, 并确保系统安装了一个make实用程序 (Gnu 使在不同地点的互联网上免费提供)。在 TestDemo, 取消注释test3 (), 然后键入做和观察结果。
2. 通过添加引发异常的新测试来修改 TestDemo。键入制作并观察结果。
3. 通过添加单元测试, 修改1章中的练习的解决方案。编写包含单元测试的 makefiles。