# 218
### DataService1
```java
package com.in28minutes.learnSpringAopK1.aopexample.data;

import org.springframework.stereotype.Repository;

@Repository
public class DataService1 {

	public int[] retrieveData() {
		return new int[] {11,22,33,44,55};
	}
}
```
### BusinessService1
```java
package com.in28minutes.learnSpringAopK1.aopexample.business;

import java.util.Arrays;

import org.springframework.stereotype.Service;

import com.in28minutes.learnSpringAopK1.aopexample.data.DataService1;

@Service
public class BusinessService1 {
	
	private DataService1 dataService1;

	public BusinessService1(DataService1 dataService1) {
		super();
		this.dataService1 = dataService1;
	}
	
	public int calculateMax() {
		
		int[] data = dataService1.retrieveData();
		return Arrays.stream(data)
				.max()
				.orElse(0);
	}

}
```
### LearnSpringAopK1Application
```java
package com.in28minutes.learnSpringAopK1;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import com.in28minutes.learnSpringAopK1.aopexample.business.BusinessService1;

@SpringBootApplication
public class LearnSpringAopK1Application implements CommandLineRunner {

	private BusinessService1 businessService1;
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	public LearnSpringAopK1Application(BusinessService1 businessService1) {
		super();
		this.businessService1 = businessService1;
	}

	public static void main(String[] args) {
		SpringApplication.run(LearnSpringAopK1Application.class, args);
	}

	@Override
	public void run(String... args) throws Exception {
		logger.info("Value return is {}",businessService1.calculateMax());		
	}

}
```
--------------
---------
# 219
### build.gradle
```properties
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.4.0'
	id 'io.spring.dependency-management' version '1.1.6'
}

group = 'com.in28minutes'
version = '0.0.1-SNAPSHOT'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(17)
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter'
	implementation 'org.springframework.boot:spring-boot-starter-aop'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

tasks.named('test') {
	useJUnitPlatform()
}
```
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	//execution(* com.in28minutes.learnSpringAopK1.aopexample.business.*.*(..))
	@Before("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("Before Aspect - Method is called - {}",joinPoint);
	}

}
```
------
---------
# 220
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	//execution(* com.in28minutes.learnSpringAopK1.aopexample.business.*.*(..))
	@Before("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\nBefore Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}

}
```
-------
---------
# 221
## @After
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Before("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\nBefore Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}
	
	@After("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCallAfterExecution(JoinPoint joinPoint) {
		
		logger.info("\n  After Aspect: {} has executed ",joinPoint);
	}

}
```
## @AfterThrowing
### BusinessService1
```java
package com.in28minutes.learnSpringAopK1.aopexample.business;

import org.springframework.stereotype.Service;

import com.in28minutes.learnSpringAopK1.aopexample.data.DataService1;

@Service
public class BusinessService1 {
	
	private DataService1 dataService1;

	public BusinessService1(DataService1 dataService1) {
		super();
		this.dataService1 = dataService1;
	}
	
	public int calculateMax() {
		
		int[] data = dataService1.retrieveData();
		
		throw new RuntimeException("Something went Wrong!");
		
//		return Arrays.stream(data).max().orElse(0);
	}

}
```
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Before("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\nBefore Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}
	
	@After("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCallAfterExecution(JoinPoint joinPoint) {
		
		logger.info("\n  After Aspect: {} has executed ",joinPoint);
	}

	@AfterThrowing(
	 pointcut = "execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))",
	 throwing = "exception"
	)
	public void logMethodCallAfterExeception(JoinPoint joinPoint,Exception exception) {
		
		logger.info("\n  After Throwing Aspect: {} has thrown an exception {} ",joinPoint,exception);
	}
}
```
## @AfterReturningAspect
### BusinessService1
```java
package com.in28minutes.learnSpringAopK1.aopexample.business;

import java.util.Arrays;

import org.springframework.stereotype.Service;

import com.in28minutes.learnSpringAopK1.aopexample.data.DataService1;

@Service
public class BusinessService1 {
	
	private DataService1 dataService1;

	public BusinessService1(DataService1 dataService1) {
		super();
		this.dataService1 = dataService1;
	}
	
	public int calculateMax() {
		
		int[] data = dataService1.retrieveData();
		
//		throw new RuntimeException("Something went Wrong!");
		
		return Arrays.stream(data).max().orElse(0);
	}

}
```
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Before("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\nBefore Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}
	
	@After("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void logMethodCallAfterExecution(JoinPoint joinPoint) {
		
		logger.info("\n  After Aspect: {} has executed ",joinPoint);
	}

	@AfterThrowing(
	 pointcut = "execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))",
	 throwing = "exception"
	)
	public void logMethodCallAfterExeception(JoinPoint joinPoint,Exception exception) {
		
		logger.info("\n  After Throwing Aspect: {} has thrown an exception {} ",joinPoint,exception);
	}
	
	@AfterReturning(
			 pointcut = "execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))",
			 returning = "resultValue"
			)
			public void logMethodCallAfterSuccessfullExecution(JoinPoint joinPoint,Object resultValue) {
				
				logger.info("\n  After Returning Aspect: {} has return {} ",joinPoint,resultValue);
			}
}
```
------
---
# 222
### PerformanceAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class PerformanceAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Around("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public Object findExecutionTime(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
		
		long startTimeMillis = System.currentTimeMillis();
		
		Object returnValue = proceedingJoinPoint.proceed();
		
		long stopTimeMillis = System.currentTimeMillis();
		
		long executionDuration = stopTimeMillis - startTimeMillis;
		
		logger.info("\n =1* At Around Aspect - this {} method executed in {} ms time",proceedingJoinPoint,executionDuration);

		return returnValue;
	}

}
```
## 2 Increase Execution time in calculateMax()
### BusinessService1
```java
package com.in28minutes.learnSpringAopK1.aopexample.business;

import java.util.Arrays;

import org.springframework.stereotype.Service;

import com.in28minutes.learnSpringAopK1.aopexample.data.DataService1;

@Service
public class BusinessService1 {
	
	private DataService1 dataService1;

	public BusinessService1(DataService1 dataService1) {
		super();
		this.dataService1 = dataService1;
	}
	
	public int calculateMax() {
		
		int[] data = dataService1.retrieveData();
		
		try {
			Thread.sleep(30);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
//		throw new RuntimeException("Something went Wrong!");
		
		return Arrays.stream(data).max().orElse(0);
	}

}
```
## 3 Implement same logic in dataService()
###
```java
package com.in28minutes.learnSpringAopK1.aopexample.data;

import org.springframework.stereotype.Repository;

@Repository
public class DataService1 {

	public int[] retrieveData() {
		try {
			Thread.sleep(30);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		return new int[] {11,22,33,44,55};
	}
}
```
## 4. Exploring Furthur via creating new Business and Data Service
### DataService2
```java
package com.in28minutes.learnSpringAopK1.aopexample.data;

import org.springframework.stereotype.Repository;

@Repository
public class DataService2 {

	public int[] retrieveData() {
		try {
			Thread.sleep(30);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		return new int[] {111,222,333,444,555};
	}
}
```
### BusinessService2
```java
package com.in28minutes.learnSpringAopK1.aopexample.business;

import java.util.Arrays;

import org.springframework.stereotype.Service;

import com.in28minutes.learnSpringAopK1.aopexample.data.DataService2;

@Service
public class BusinessService2 {
	
	private DataService2 dataService2;

	public BusinessService2(DataService2 dataService2) {
		super();
		this.dataService2 = dataService2;
	}
	
	public int calculateMin() {
		
		int[] data = dataService2.retrieveData();
		
		try {
			Thread.sleep(30);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
//		throw new RuntimeException("Something went Wrong!");
		
		return Arrays.stream(data).min().orElse(0);
	}

}
```
### LearnSpringAopK1Application
```java
package com.in28minutes.learnSpringAopK1;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import com.in28minutes.learnSpringAopK1.aopexample.business.BusinessService1;
import com.in28minutes.learnSpringAopK1.aopexample.business.BusinessService2;

@SpringBootApplication
public class LearnSpringAopK1Application implements CommandLineRunner {

	private BusinessService1 businessService1;
	private BusinessService2 businessService2;
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	public LearnSpringAopK1Application(BusinessService1 businessService1,BusinessService2 businessService2) {
		super();
		this.businessService1 = businessService1;
		this.businessService2 = businessService2;
	}

	public static void main(String[] args) {
		SpringApplication.run(LearnSpringAopK1Application.class, args);
	}

	@Override
	public void run(String... args) throws Exception {
		logger.info("businessService1 Value return is {}",businessService1.calculateMax());	
		logger.info("businessService2 Value return is {}",businessService2.calculateMin());	
	}

}
```
----
--------
# 223
## 1
public class CommonPointCutConfig {
### 
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.annotation.Pointcut;

public class CommonPointCutConfig {
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void businessAndDataPackageConfig() {}

}
```
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Before("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\n  Before Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}
	
	@After("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()")
	public void logMethodCallAfterExecution(JoinPoint joinPoint) {
		
		logger.info("\n  After Aspect: {} has executed ",joinPoint);
	}

	@AfterThrowing(
	 pointcut = "com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()",
	 throwing = "exception"
	)
	public void logMethodCallAfterExeception(JoinPoint joinPoint,Exception exception) {
		
		logger.info("\n   After Throwing Aspect: {} has thrown an exception {} ",joinPoint,exception);
	}
	
	@AfterReturning(
			 pointcut = "com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()",
			 returning = "resultValue"
			)
			public void logMethodCallAfterSuccessfullExecution(JoinPoint joinPoint,Object resultValue) {
				
				logger.info("\n   After Returning Aspect: {} has return {} ",joinPoint,resultValue);
			}
}
```
### PerformanceAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class PerformanceAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Around("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()")
	public Object findExecutionTime(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
		
		long startTimeMillis = System.currentTimeMillis();
		
		Object returnValue = proceedingJoinPoint.proceed();
		
		long stopTimeMillis = System.currentTimeMillis();
		
		long executionDuration = stopTimeMillis - startTimeMillis;
		
		logger.info("\n At Around Aspect - this {} method executed in {} ms time",proceedingJoinPoint,executionDuration);

		return returnValue;
	}

}
```
## 2.
### CommonPointCutConfig
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.annotation.Pointcut;

public class CommonPointCutConfig {
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void businessAndDataPackageConfig() {}
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.business.*.*(..))")
	public void businessPackageConfig() {}
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.data.*.*(..))")
	public void dataPackageConfig() {}

}
```
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Before("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessPackageConfig()")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\n  Before Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}
	
	@After("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()")
	public void logMethodCallAfterExecution(JoinPoint joinPoint) {
		
		logger.info("\n  After Aspect: {} has executed ",joinPoint);
	}

	@AfterThrowing(
	 pointcut = "com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()",
	 throwing = "exception"
	)
	public void logMethodCallAfterExeception(JoinPoint joinPoint,Exception exception) {
		
		logger.info("\n   After Throwing Aspect: {} has thrown an exception {} ",joinPoint,exception);
	}
	
	@AfterReturning(
			 pointcut = "com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.dataPackageConfig()",
			 returning = "resultValue"
			)
			public void logMethodCallAfterSuccessfullExecution(JoinPoint joinPoint,Object resultValue) {
				
				logger.info("\n  After Returning Aspect: {} has return {} ",joinPoint,resultValue);
			}
}
```
## 3
### CommonPointCutConfig
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.annotation.Pointcut;

public class CommonPointCutConfig {
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.*.*.*(..))")
	public void businessAndDataPackageConfig() {}
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.business.*.*(..))")
	public void businessPackageConfig() {}
	
	@Pointcut("execution(* com.in28minutes.learnSpringAopK1.aopexample.data.*.*(..))")
	public void dataPackageConfig() {}
	
	
	@Pointcut("bean(*Service*)")
	public void allPackageConfigUsingBean() {}

}
```
### LoggingAspect
```java
package com.in28minutes.learnSpringAopK1.aopexample.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;

@Configuration
@Aspect
public class LoggingAspect {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@Before("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.allPackageConfigUsingBean()")
	public void logMethodCall(JoinPoint joinPoint) {
		
		logger.info("\n  Before Aspect : -{} Method is called "
				+ "\n with arguments : - {}",joinPoint,joinPoint.getArgs());
	}
	
	@After("com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()")
	public void logMethodCallAfterExecution(JoinPoint joinPoint) {
		
		logger.info("\n  After Aspect: {} has executed ",joinPoint);
	}

	@AfterThrowing(
	 pointcut = "com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.businessAndDataPackageConfig()",
	 throwing = "exception"
	)
	public void logMethodCallAfterExeception(JoinPoint joinPoint,Exception exception) {
		
		logger.info("\n   After Throwing Aspect: {} has thrown an exception {} ",joinPoint,exception);
	}
	
	@AfterReturning(
			 pointcut = "com.in28minutes.learnSpringAopK1.aopexample.aspect.CommonPointCutConfig.dataPackageConfig()",
			 returning = "resultValue"
			)
			public void logMethodCallAfterSuccessfullExecution(JoinPoint joinPoint,Object resultValue) {
				
				logger.info("\n  After Returning Aspect: {} has return {} ",joinPoint,resultValue);
			}
}
```
--------
------
# 233
```
1.	docker container run -d -p 5000:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
2.	docker container run -d -p 5000:5000 in28min/hello-world-java:0.0.1.RELEASE
3.	docker container run -d -p 5000:5000 in28min/hello-world-python:0.0.1.RELEASE
4.	docker container ls 
5.	docker image ls
6.	docker container stop cc
7.	docker container run -d -p 5001:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
8.	docker container run -d -p 5002:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
9.	docker container run -p 5003:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
10.	docker container run -p 5003:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
11.	 
12.	docker --version
13.	docker container ls
14.	docker build -t in28min/hello-world-docker:v1 .
15.	docker image list
16.	docker run -d -p 5000:5000 in28min/hello-world-docker:v1
17.	docker build -t in28min/hello-world-docker:v2 .
18.	docker container run -d -p 5000:5000 in28min/hello-world-docker:v2
19.	docker build -t in28min/hello-world-docker:v3 .
20.	docker container run -d -p 5000:5000 in28min/hello-world-docker:v3
21.	docker build -t in28min/hello-world-docker:v4 .
```
---
-----
# 234 
```java
docker --version

docker container run -d -p 5000:5000 in28min/hello-world-python:0.0.1.RELEASE
```
----
-----
# 235
```java
docker container ls
or 
docker container list

docker container stop 46e
or
docker container stop 46ed1f7f3db0 

docker container run -d -p 5000:5000 in28min/hello-world-nodejs:0.0.1.RELEASE
```
------
------
# 239
### HelloWorldController
```java
package com.in28min.rest.webservices;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloWorldController {
	
	@GetMapping(path = "/")
	public String helloWorld() {
		return "{\"message\":\"Hello World Java Adi v11\"}";
	}
}
```
### app.prop
```properties
spring.application.name=hello-world-java2
logging.level.org.springframework=debug
server.port=5000
```
### Dockerfile
```file
FROM openjdk:17.0-slim 
COPY target/*.jar app.jar 
EXPOSE 5000 
ENTRYPOINT ["java","-jar","/app.jar"]
```
### Commands
```properties

# For building image
- We have to go to specific folder and run the bellow command

C:\Users\Asus\OneDrive\Desktop\ReviseNotes\Spring3\Ranga_Spring3\Execution_02\Docker\1\hello-world-java2\hello-world-java2>docker build -t in28min/hello-world-docker:v11 .

# version
docker --version

# for image list
docker image list or docker image ls

# for running docker image
- We have to go to specif folder and run bellow command.
C:\Users\Asus\OneDrive\Desktop\ReviseNotes\Spring3\Ranga_Spring3\Execution_02\Docker\1\hello-world-java2\hello-world-java2>docker run -d -p 5001:5000 in28min/hello-world-docker:v11

# for docker container list
docker container ls or docker container list
```
---------
---------
# 240
###  HelloWorldController
```java
package com.in28min.rest.webservices;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloWorldController {
	
	@GetMapping(path = "/")
	public String helloWorld() {
		return "{\"message\":\"Hello World Java Adi v12\"}";
	}
}
```
### Dockerfile
```properties
FROM maven:3.8.6-openjdk-18-slim AS build 
WORKDIR /home/app 
COPY . /home/app 
RUN mvn -f /home/app/pom.xml clean package

FROM openjdk:18-slim
EXPOSE 5001 
COPY --from=build /home/app/target/*.jar app.jar
ENTRYPOINT [ "sh", "-c", "java -jar /app.jar" ]
```
### Commands
```properties

# building docker image
docker build -t in28min/hello-world-docker:v12 .

# Running docker image
docker container run -d -p 5001:5000 in28min/hello-world-docker:v12
```
-------
-------






