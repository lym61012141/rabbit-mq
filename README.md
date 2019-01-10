### spring-boot 2.0.4 RabbitMq初次尝试

####  一 direct模式上,routingKey和bindingKey必须保持一致才可以将消息发送到指定的队列上,是一对一的模式
 
####  二 在topic模式上,bindingKey可以以通配符的方式描述,routingKey是指定的字符,是多对多的方式

####  三 交换机绑定队列时用的bindingKey 和 发送消息时候用的routingKey保持一致（direct exchange）或者匹配规则一致（topic exchange）就可以将消息发送到该队列上;其中fanoutExchange是广播，只绑定队列即可，不用指定bindingKey
_这里使用的绑定交换机的方法给人误区:with里面的参数其实叫做:bindingKey,生产者在发送消息时指定的叫做:routingKey,
  bindingKey支持通配符,使用场景如下(*和#号都可以代表空的单词)_
1. 注意绑定交换机时的bindingKey的命名规则是点号分割英文单词,
2. bindingKey有两种通配符
   1. "#" :可以匹配多个单词
   2. "*" :只能匹配一个单词
3. 通配符的使用规则如下:
-  (1)一个生产者匹配多个交换机,进而将消息发送多个队列上
    1. 生产者: P1->routingKey :1.2.3
    2. 交换机: X ->bindingKey :1.*.3->Q1, *.2.3->Q2, 1.2.*->Q3, 1.#->Q4, #.3->Q5 ...
     这样一个生产者就可以将消息发送多个队列上
-  (2)多个生产者匹配一个交换机,进而将多个生产者生产的消息发送到同一个队列中
    1. 生产者: P1->routingKey :1.2, P2->1.2.3
    2. 交换机: X ->bindingKey :1.#->Q
    3. 这样多个生产者就可以将他们生产的消息发送到同一个队列中
####  三 生产者不绑定交换机的时候
1. 生产者不绑定交换机,那么只有routingKey,bindingKey和队列名三者名字完全一致的时候(并且无论是哪种类型的交换机通过BindingBuilder创建的Binding,必须要存在一个符合该条件的)才可以发送到队列,否者直接丢弃该生产消息
