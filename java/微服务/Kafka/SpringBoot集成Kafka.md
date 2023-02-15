消息生产和消费
配置文件：
```properties
logging.level.root=info  
  
spring.kafka.producer.bootstrap-servers=192.168.0.120:9092  
  
spring.kafka.consumer.bootstrap-servers=192.168.0.120:9092
```

```java
@SpringBootApplication  
@RestController  
@RequestMapping  
public class KafkaDemo02Application {  
    private static final Logger logger =  LoggerFactory.getLogger(KafkaDemo02Application.class);  
  
    public static void main(String[] args) {  
        SpringApplication.run(KafkaDemo02Application.class, args);  
    }  
    @RequestMapping("index")  
    public String index() {  
        return "hello kafka!";  
    }  
    @Autowired  
    private KafkaTemplate template;  
    private static final String topic = "ithema";  
  
    /**  
     * 消息的生产者  
     * @param input  
     * @return  
     */    @GetMapping("send/{input}")  
    public String sendToKafka(@PathVariable String input) {  
        this.template.send(topic, input);  
        return input + " Send success!";  
    }  
      
    /**  
     * 消息的消费者  
     * @param input  
     */  
    @KafkaListener(id = "myContainer1", topics = topic, groupId = "group.demo")  
    public void listener(String input) {  
        logger.info("message input value:{}", input);  
    }  
}
```

事务
方式一

```properties
spring.kafka.producer.transaction-id-prefix=kafka_tx.
```

```java
@GetMapping("send/{input}")  
public String sendToKafka(@PathVariable String input) {  
   // this.template.send(topic, input);  
  
    template.executeInTransaction(t -> {  
        t.send(topic, input);  
        if ("error".equals(input)) {  
            throw new RuntimeException("input is error!");  
        }  
        t.send(topic, input + " anthor");  
        return true;  
    });  
  
  
    return input + " Send success!";  
}
```

方式二
```java
@GetMapping("send2/{input}")  
@Transactional(rollbackFor = RuntimeException.class)  
public String sendToKafkaTran(@PathVariable String input) {  
    template.send(topic, input);  
    if ("error".equals(input)) {  
        throw new RuntimeException("input is error!");  
    }  
    template.send(topic, input + " anthor");  
    return input + " Send success!";  
}
```
