---
layout: post
description: >
  가장 간단한 스프링 배치 예제를 알아보고 사용해보자.
author: cgkim449
---

# [Spring Batch] 자정마다 잔디 그래프에 빈 네모칸 하나씩 추가하기
![image](https://user-images.githubusercontent.com/68311318/134714410-4870ab6d-a712-4969-82fc-9f152c6ec4e5.png)  
자정마다 빈 네모칸을 하나 생성하고 가장 오래된 네모칸을 삭제하려면 `스프링 배치`가 필요하다.

## 배치란?
배치란 일괄 처리 작업을 말한다. `Spring Framework`에는 배치가 없었는데, `Spring Boot`에서 추가되었다.  

## 들어가기 전에
스프링 배치 예제를 사용하기 전에 알아야 하는 최소한의 내용은 다음과 같다.
- 스프링 배치 메타 테이블
- `Job`, `Step`, `Tasklet` 클래스

### 스프링 배치 메타 테이블
배치 작업을 할 때 예외가 발생하거나 또는 배치 작업을 언제/어떻게 했는지에 대한 정보를 테이블에 남겨야하는데, 이때 사용하는 테이블을 **스프링 배치 메타 테이블**이라고 한다.  
![image](https://user-images.githubusercontent.com/68311318/134692795-30bbad45-42ef-46ae-b2b6-d39aa4e501aa.png)  
출처: [https://docs.spring.io](https://docs.spring.io)  

DB에 따라 메타 테이블의 DDL에 차이가 있다. Oracle에서는 시퀀스를 사용하나, MySQL에서는 시퀀스를 사용하지 않기 때문이다.  
아래 링크는 Oracle DB를 위한 메타 테이블 DDL이다.  
[https://github.com/spring-projects/spring-batch/blob/main/spring-batch-core/src/main/resources/org/springframework/batch/core/schema-oracle10g.sql](https://github.com/spring-projects/spring-batch/blob/main/spring-batch-core/src/main/resources/org/springframework/batch/core/schema-oracle10g.sql)

### Job, Step, Tasklet
스프링이 제공하는 클래스이다.  
- `Job`은 일괄 처리해야할 작업 전체라고 생각하면된다. 그리고 그 중 코어가 `Tasklet`이다.  
- `Job`은 `Step`으로 이루어져있고, `Step`은 `Tasklet`으로 이루어져있다고 생각하면 된다. 

## 스프링 배치의 가장 간단한 예제
[https://deeplify.dev/back-end/spring/batch-tutorial#%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B0%B0%EC%B9%98-%EC%98%88%EC%A0%9C](https://deeplify.dev/back-end/spring/batch-tutorial#%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B0%B0%EC%B9%98-%EC%98%88%EC%A0%9C)  
에 스프링 배치의 가장 간단한 예제가 있는데  
- 우리는 Oracle DB를 사용하므로 `application.properties`에 데이터베이스 설정만 아래처럼 해주면 된다.  

```yaml
spring.sql.init.schema-locations=classpath:/org/springframework/batch/core/schema-oracle10g.sql 
# 위에 썼듯이 메타테이블 DDL이다.
spring.batch.job.enabled: false 
#true이면 프로젝트 실행시 무조건 배치 작업을 한번 한다.
```
- 그리고나서 우리가 할건 `execute()`에 우리의 로직을 추가하는 것 밖에 없다!  
나는 잔디 그래프에 자정마다 빈 네모 칸을 하나 추가하고 가장 오래된 네모칸을 삭제하는 로직을 추가해봤다.  

```java
package com.team.interview.batch.tasklets;

import org.springframework.batch.core.StepContribution;
import org.springframework.batch.core.scope.context.ChunkContext;
import org.springframework.batch.core.step.tasklet.Tasklet;
import org.springframework.batch.repeat.RepeatStatus;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import com.team.interview.dao.ProfileDAO;
import com.team.interview.vo.ProfileVO;

@Component
public class JandiTasklet implements Tasklet{

  @Autowired
  public ProfileDAO profileDAO;

  @Override
  public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext)
      throws Exception {
    int total = profileDAO.getMaxNumOfPfId();

    for (int i = 1; i <= total; i++) {
      ProfileVO profileVO = profileDAO.getProfile(i);
      if(profileVO == null) {
        continue;
      }
      String jandi = profileVO.getJandi();
      jandi = newDay(jandi);
      profileVO.setJandi(jandi);

      profileDAO.updateNewDayJandi(profileVO);
    }

    System.out.println("executed jandi..........");

    return RepeatStatus.FINISHED;
  }

  static String newDay(String jandi) {
    StringBuilder sb = new StringBuilder(jandi);
    int indexOfComma = sb.indexOf(",");
    sb.delete(0, indexOfComma + 1);
    sb.append(",0");
    jandi = sb.toString();

    return jandi;
  }
}
```

- CRON 표현식을 사용하여 배치 주기를 설정할 수도 있는데, 예를 들면  
`@Scheduled(cron = "* * 0 * * *")` 처럼 쓴다면 매일 자정 주기로 배치 처리를 한다는 것이다.  

## 참고
- [https://deeplify.dev/back-end/spring/batch-tutorial](https://deeplify.dev/back-end/spring/batch-tutorial)

## 추가
- 에러 해결
- 잔디 execute()할때 주의할것:new

- job, joblauncher
- job, tasklet, step, 
  - 애노테이션과 연관하여 설명
- 스프링 배치의 구조 등
