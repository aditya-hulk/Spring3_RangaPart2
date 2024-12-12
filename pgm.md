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


