# JMeter

## BeanShell

### Varialbe
* vars
* 변수 값은 `String` 타입

```
vars.get("VARIABLE_NAME");
vars.put("VARIABLE_NAME","VALUE");

String my_var = vars.get("MY_VARIABLE");
log.info("The value of my_var is " + my_var);

vars.put("VARIABLE_NAME",vars.get("VARIABLE_NAME").replace("\\/","/"));
```

### SampleResult
* ctx.getPreviousResult() or prev
   * 이전 Sampler 의 결과
   * BeanShell PostProcessor
* SampleResult
  * 현재 Sampler 의 결과 
  * BeanShell Assertion, BeanShell Sampler


### JMeter Context (변수명: `ctx`)
* ctx.getPreviousResult()
* ctx.getPreviousSampler()
* ctx.getThread()
* ctx.getThreadGroup()


### 로그
```
log.info("This is the log for INFOR level");
log.warn("This is the log for WARNING level");
log.error("This is the log for ERROR level");
```

### Console 출력

```
OUT.println("INPUT MESSAGE HERE");
```

### Properties

```
props.get("PROPERTY_NAME");
props.put("PROPERTY_NAME", "VALUE");

//update the existing property
props.put("jmeter.version","3.1")
```


### Use-case

* 강제로 오류 발생 (BeanShell Assertion 등)

```
SampleResult.setSuccessful(false);
```

### 참고
* https://octoperf.com/blog/2017/11/09/reusable-sample-jmeter-scripts/
