# 设计要点
* 统一交易框架  

* 事件驱动和时间漫步  
  * 时间漫步  
  就是在某个时间,trader主动去要数据,而不是监听,比如某个股票的财务报表分析,就适合采用这种方式,
  我可能只需要在报表发布期去分析一下,而不用一直"监听".  
  * 事件驱动  
  就是只要有某类事件发生,就告诉我.  

  这两种方式应该能够一起使用.  
* 不同标的的行情处理  
一个标的对应一个topic,对于回测来说,如果只用一个线程,收到的不同标的的行情,可能时间会相差较远,
这对多标的来说意义不大;对于实时行情,只要消费能力够强,基本上都是实时的,有对比意义.

* 交易和交易结果处理解耦  
交易应该省去不必要的操作,只做交易"必须做"的事  
* 交易账户的事务机制  
交易的变量有两个:交易标的 账户  
对交易标的的操作意味着账户的变动,必须保证账户操作的线程安全  

* 股票复权处理  
不复权,技术指标计算会有问题;  
前复权,如果再除权除息"前面的"数据会变化,这种看上去美好的方式,其实只适合静态的交易,对账户的处理并不方便.  
后复权,"现在的价格"看上去非常高,但是对于交易来说,"变化"才是关键.  
采用后复权的好处在于:历史买入的价格就是真实成本,不需要再做其他处理,也不会变化,即使明天又要给你分红送股.
而我们的交易系统是需要可以always running的,简单才是最重要的.  
后复权需要注意的问题:由于tick价格总是不复权的,操作的时候需要对其(x factor),这里面有个小问题:  
即你能买到的股数可能大幅变少.但这并不会影响收益的计算,你需要关注的是价格的变化,而不是个股的绝对价格;  
你需要关注的是你要买入多少钱,而不是多少股.

  总之:系统简单稳定最重要,而不是去迎合所谓的"真实"
