# File-Upload-Spring

# 1. 파일 업로드 소개 & 프로젝트 생성

### 파일 업로드 소개

일반적으로 사용하는 HTML Form 을 통한 파일 업로드를 이해하려면 먼저 폼을 전송하는 다음 두가지 방식의 차이를 이해해야 합니다.

### HTML 폼 전송 방식

`application/x-www-form-urlencoded` 방식

`multipart/form-data` 방식 

위처럼 두가지 방식이 있습니다.

application/x-www-form-urlencoded 방식

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1051baab-c513-4522-bfea-0f01b0b56c91/Untitled.png)

`application/x-www-urlencoded` 방식은 HTML 폼 데이터를 서버로 전송하는 가장 기본적인 방법이다. 

Form 태그에 별도의 `enctype` 옵션이 없으면 웹 브라우저는 요청 HTTP 메시지의 헤더에 다음 내용을 추가합니다.

`Content-Type: application/x-www-form-urlencoded`

그리고 폼에 입력한 항목을 HTTP Body 에 문자로 `username=kim&age=20` 와 같이 `‘&’` 로 구분해서 전송한다.

파일을 업로드 하려면 파일은 문자가 아니라 바이너리 데이터를 전송해야 한다. 문자를 전송하는 이 방식으로 파일을 전송하기는 어렵다. 

그리고 또 한가지 문제가 더 있는데, **보통 폼을 전송할 때 파일만 전송하는 것이 아니라는 점입니다.**

다음 예를 봅시다.

```html
- 이름
- 나이
- 첨부파일
```

여기에서 이름과 나이도 전송해야 하고, 첨부파일도 함께 전송해야 합니다. 문제는 이름과 나이는 문자로 전송하고, 첨부파일은 바이너리로 전송해야 한다는 점입니다. 여기에서 문제가 발생합니다. 

**문자와 바이너리를 동시에 전송해야 하는 상황입니다.**

이 문제를 해결하기 위해 HTTP 는 `multipart/form-data` 라는 전송 방식을 제공합니다.

multipart/form-data 방식

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd64ecfb-dc63-4c6a-a675-cbf1efec3d98/Untitled.png)

이 방식을 사용하려면 Form 태그에 별도의 `enctype=”multipart/form-data”` 를 지정해야 합니다.

`multipart/form-data` 방식은 다른 종류의 여러 파일과 폼의 내용을 함께 전송할 수 있습니다. (이름부터 `multipart` 임.)

폼의 입력 결과로 생성된 HTTP 메시지를 보면 각각의 전송 항목이 구분이 되어 있습니다. 

`Content-Disposition` 이라는 항목별 헤더가 추가되어 있고 여기에 부가 정보가 있습니다. 예제에서는 `username`, `age`, `file1` 이 각각 분리되어 있고, 폼의 일반 데이터는 각 항목별로 문자가 전송되고, 파일의 경우 파일 이름과 `Content-Type` 이 추가되고 바이너리 데이터가 전송됩니다.

`multipart/form-data` 는 이렇게 각각의 항목을 구분해서 한번에 전송하는 것입니다.

### Part

`multipart/form-data` 는 `application/x-www-form-urlencoded` 와 비교해서 매우 복잡하고 각각의 `Part` 로 나누어져 있습니다. 그렇다면 이렇게 복잡한 HTTP 메시지를 서버에서 어떻게 사용할 수 있을까요?

> 참고 - `multipart/form-data` 와 폼 데이터 전송에 대한 더 자세한 내용은 Http 웹 지식 카테고리를 참고합시다.
> 

### 프로젝트 생성

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/05581ff8-7270-4785-a582-98a74902c94a/Untitled.png)

그 후 생성된 압축파일의 압축을 풀고 나서 `build.gradle` 파일을 open as project 합니다.

### build.gradle 확인

```groovy
plugins {
	id 'java'
	id 'org.springframework.boot' version '2.7.8'
	id 'io.spring.dependency-management' version '1.0.15.RELEASE'
}

group = 'hello'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}
```

기본 메인 클래스(`UploadApplication.main()`) 을 실행해서 동작을 확인합니다.

[http://localhost:8080](http://localhost:8080) 호출해서 Whitelabel Error Page 가 나오면 정상 동작하는 것입니다.

편의상 `index.html` 을 추가해두자.

`resources/static/index.html`

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>상품 관리
        <ul>
            <li><a href="/servlet/v1/upload">서블릿 파일 업로드1</a></li>
            <li><a href="/servlet/v2/upload">서블릿 파일 업로드2</a></li>
            <li><a href="/spring/upload">스프링 파일 업로드</a></li>
            <li><a href="/items/new">상품 - 파일, 이미지 업로드</a></li>
        </ul>
    </li>
</ul>
</body>
</html>
```

# 2. 서블릿과 파일 업로드 1

먼저 서블릿을 통한 파일 업로드를 코드와 함께 알아봅시다.

`ServletUploadControllerV1`

```java
package hello.upload.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.Part;
import java.io.IOException;
import java.util.Collection;

@Slf4j
@Controller
@RequestMapping("/servlet/v1")
public class ServletUploadController {

    @GetMapping("/upload")
    public String newFile() {
        return "upload-form";
    }

    @PostMapping("/upload")
    public String saveFileV1(HttpServletRequest request) throws ServletException, IOException {
        log.info("request={}", request);
        String itemName = request.getParameter("itemName");
        log.info("itemName={}", itemName);
        Collection<Part> parts = request.getParts();
        log.info("parts={}", parts);
        return "upload-form";
    }

}
```

`request.getParts()` : `multipart/form-data` 전송 방식에서 각각 나누어진 부분을 받아서 확인할 수 있다.

`resources/templates/upload-form.html`

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
</head>
<body>
<div class="container">
    <div class="py-5 text-center">
        <h2>상품 등록 폼</h2>
    </div>
    <h4 class="mb-3">상품 입력</h4>
    <form th:action method="post" enctype="multipart/form-data">
        <ul>
            <li>상품명 <input type="text" name="itemName"></li>
            <li>파일<input type="file" name="file"></li>
        </ul>
        <input type="submit"/>
    </form>
</div> <!-- /container -->
</body>
</html>
```

테스트를 진행하기 전에 먼저 다음 옵션들을 추가합시다.

`application.properties`

```html
logging.level.org.apache.coyote.http11=debug
```

이 옵션을 사용하면 HTTP 요청 메시지를 확인할 수 있습니다.

[http://localhost:8080/servlet/v1/upload](http://localhost:8080/servlet/v1/upload)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eae10b43-e57e-4bf6-9a12-903e5cf8d025/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f5cd1306-2f03-452b-928f-d83e55e54b2c/Untitled.png)

실행해보면 `logging.level.org.apache.coyote.http11` 옵션을 통한 로그에서 `multipart/form-data` 방식으로 전송된 것을 확인할 수 있습니다.

### 결과 로그

```html
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryHeYjVVf1gIBqEnPE

........
Content-Disposition: form-data; name="itemName"

spring
------WebKitFormBoundary.....
Content-Disposition: form-data; name="file"; filename="Untitled.png"
Content-Type: image/png
```

### 멀티 파트 사용 옵션

`[application.properties](http://application.properties)` - 업로드 사이즈 제한

```html
spring.servlet.multipart.max-file-size=1MB
spring.servlet.multipart.max-request-size=10MB
```

큰 파일을 무제한 업로드하게 둘 수는 없으므로 업로드 사이즈를 제한할 수 있습니다.

사이즈를 넘으면 `SizeLimitExceedException` 이 발생합니다.

`max-file-size`: 파일 하나의 최대 사이즈는 기본 1MB 입니다.

`max-request-size` 멀티파트 요청 하나에 여러 파일을 업로드 할 수 있는데 그 전체 합입니다. 기본 10MB 입니다.

### spring.servlet.multipart.enabled 끄기

`spring.servlet.multipart.enabled=false`

결과 로그

```html
request=org.apache.catalina.connector.RequestFacade@41cedcd
itemName=null
parts=[]
```

멀티파트는 일반적인 폼 요청인 `application/x-www-form-urlencoded` 보다 훨씬 복잡합니다.

`spring.servlet.multipart.enabled` 옵션을 끄면 서블릿 컨테이너는 멀티파트와 관련된 처리를 하지 않는다. 

그래서 결과 로그를 보면 `request.getParameter(”itemName”)`, `request.getParts()` 의 결과가 비어있습니다.

### spring.servlet.multipart.enabled 켜기

`spring.servlet.multipart.enabled=true` (기본 true)

이 옵션을 켜면 스프링 부트는 서블릿 컨테이너에게 멀티파트 데이터를 처리하라고 설정합니다. 참고로 기본값은 `true` 입니다.

```html
...
request=org.springframework.web.multipart.support.StandardMultipartHttpServletRequest@23c76edc
itemName=spring
parts=[org.apache.catalina.core.ApplicationPart@6103e1a2, org.apache.catalina.core.ApplicationPart@7f43e938]
...
```

( 마지막 줄은 `parts=[ApplicationPart1, ApplicationPart2]` 와 같음 )

`request.getParameter(”itemName”)` 의 결과도 잘 출력하고, `request.getParts()` 에도 요청한 두 가지 멀티파트의 부분 데이터가 포함된 것을 확인할 수 있습니다. 이 옵션을 켜면 복잡한 멀티파트 요청을 처리해서 사용할 수 있게 제공합니다.

로그를 보면 `HttpServletRequest` 객체가 `RequestFacade` → `StandardMultipartHttpServletRequest` 로 변한 것을 확인할 수 있습니다.

### 참고

`spring.servlet.multipart.enabled` 옵션을 켜면 스프링의 `DIspatcherServlet` 에서 멀티파트 리졸버(`MultipartResolver`) 을 실행합니다.

멀티파트 리졸버는 멀티파트 요청인 경우 서블릿 컨테이너가 전달하는 일반적인 `HttpServletRequest` 을 `MultipartHttpServletRequest` 로 변환해서 반환합니다.

`MultipartHttpServletRequest` 는 `HttpServletRequest` 의 자식 인터페이스이고, 멀티파트와 관련된 추가 기능을 제공합니다.

스프링이 제공하는 기본 멀티파트 리졸버는 `MultipartHttpServletRequest` 인터페이스를 구현한 `StandardMultipartHttpServletRequest` 을 반환합니다.

이제 컨트롤러에서 `HttpServletRequest` 대신에 `MultipartHttpServletRequest` 을 주입받을 수 있는데, 이것을 사용하면 멀티파트와 관련된 여러가지 처리를 편리하게 할 수 있습니다. 그런데 이후 강의에서 설명할 `MultipartFile` 이라는 것을 사용하는 것이 더 편하기 때문에 `MultipartHttpServletRequest` 을 잘 사용하지는 않습니다. 더 자세한 내용은 `MultipartResolver` 을 검색해 봅시다.


# 3. 서블릿과 파일 업로드 2

서블릿이 제공하는 `Part` 에 대해서 알아보고 실제 파일도 서버에 업로드해 봅시다.

먼저 파일을 업로드하려면 실제 파일이 저장되는 경로가 필요합니다.

해당 경로에 실제 폴더를 만들어 둡시다. 그리고 다음에 만들어진 경로를 입력해 둡시다.

`application.properties`

```html
file.dir=파일 업로드 경로 설정(예): /Users/javaSpring/uploadStudy
```

주의

꼭 해당 경로에 실제 폴더를 미리 만들어 둡시다.

[`application.properties`](http://application.properties) 에서 설정할 때 마지막에 `‘/’`(슬래시)가 포함된 것에 주의합시다.

`ServletUploadControllerV2`

```java
package hello.upload.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.Part;
import java.io.IOException;
import java.io.InputStream;
import java.nio.charset.StandardCharsets;
import java.util.Collection;

@Slf4j
@Controller
@RequestMapping("/servlet/v2")
public class ServletUploadControllerV2 {

    @Value("${file.dir}")
    private String fileDir;

    @GetMapping("/upload")
    public String newFile() {
        return "upload-form";
    }

    @PostMapping("/upload")
    public String saveFileV1(HttpServletRequest request) throws ServletException, IOException {
        log.info("request={}", request);
        String itemName = request.getParameter("itemName");
        log.info("itemName={}", itemName);
        Collection<Part> parts = request.getParts();
        log.info("parts={}", parts);

        for (Part part : parts) {
            log.info("==== PART ====");
            log.info("name={}", part.getName());
            Collection<String> headerNames = part.getHeaderNames();
            for (String headerName : headerNames) {
                log.info("header {}: {}", headerName,
                        part.getHeader(headerName));
            }
            //편의 메서드
            //content-disposition; filename
            log.info("submittedFileName={}", part.getSubmittedFileName());
            log.info("size={}", part.getSize()); //part body size
            //데이터 읽기
            InputStream inputStream = part.getInputStream();
            String body = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
            log.info("body={}", body);
            //파일에 저장하기
            if (StringUtils.hasText(part.getSubmittedFileName())) {
                String fullPath = fileDir + part.getSubmittedFileName();
                log.info("파일 저장 fullPath={}", fullPath);
                part.write(fullPath);
            }
        }
        return "upload-form";
    }

}
```

```java
@Value("${file.dir}")
private String fileDir;
```

[`application.properties`](http://application.properties) 에서 설정한 file.dir 의 값을 주입합니다.

멀티파트 형식은 전송 데이터를 하나하나 각각 `Part` 로 나누어 전송합니다. `parts` 에는 이렇게 나누어진 데이터가 각각 담깁니다.

서블릿이 제공하는 `Part` 는 멀티파트 형식을 편리하게 읽을 수 있는 다양한 메서드를 제공합니다.

### Part 주요 메서드

`part.getSubmittedFileName():` 클라이언트가 전달한 파일명

`part.getInputStream()`: `Part` 의 전송 데이터를 읽을 수 있다.

`part.write(…)`: `Part` 를 통해 전송된 데이터를 저장할 수 있습니다.

실행

`http://localhost:8080/servlet/v2/upload`

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/44451795-e45b-4a2b-b6a3-1900e839279e/Untitled.png)

다음 내용을 전송했습니다.

`itemName`: itemC

`file`: Untitled.png

결과 로그

```java
==== PART ====
name=itemName
header content-disposition: form-data; name="itemName"
submittedFileName=null
size=5
body=itemC
==== PART ====
name=file
header content-disposition: form-data; name="file"; filename="Untitled.png"
header content-type: image/png
submittedFileName=Untitled.png
size=1007476
body=�PNG
파일 저장 fullPath=/Users/simjihun/javaSpringPdf/uploadStudy/Untitled.png
```

파일 저장 경로에 가보면 실제 파일이 저장된 것을 확인할 수 있습니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d75b324f-4967-459c-b38b-b89be4cd3934/Untitled.png)

참고 - 큰 용량의 파일 업로드를 테스트할 때는 로그가 너무 많이 남아서 다음 옵션을 끄는 것이 좋습니다.

`logging.level.org.apache.coyote.http11=debug`

다음 부분도 파일의 바이너리 데이터를 모두 출력하므로 끄는 것이 좋습니다.

`log.info(”body={}”, body);`

서블릿이 제공하는 `Part` 는 편하기는 하지만, `HttpServletRequest` 을 사용해야 하고, 추가로 파일 부분만 구분하려면 여러가지 코드를 넣어야 합니다.

그렇다면 다음 글에서 스프링이 이 부분을 얼마나 편리하게 제공하는지를 확인해봅시다.

# 4. 스프링과 파일 업로드

스프링은 `MultipartFile` 이라는 인터페이스로 멀티파트 파일을 매우 편리하게 지원합니다.

`SpringUploadController`

```java
package hello.upload.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;

@Slf4j
@Controller
@RequestMapping("/spring")
public class SpringUploadController {

    @Value("${file.dir")
    private String fileDir;

    @GetMapping("/upload")
    public String newFile() {
        return "upload-form";
    }

    @PostMapping("/upload")
    public String saveFile(@RequestParam String itemName,
                           @RequestParam MultipartFile file,
                           HttpServletRequest request) throws IOException {
        log.info("request={}", request);
        log.info("itemName={}", itemName);
        log.info("multipartFile={}", file);

        if (!file.isEmpty()) {
            String fullPath = fileDir + file.getOriginalFilename();
            log.info("파일 저장 fullPath={}", fullPath);
            file.transferTo(new File(fullPath));
        }
        return "upload-form";
    }
}
```

스프링 덕분에 필요한 부분의 코드만 작성하면 됩니다.

`@RequestParam MultipartFile file`

업로드하는 HTML Form 의 name 에 맞추어 `@RequestParam` 을 적용하면 됩니다. 추가로 `@ModelAttribute` 에서도 `MultipartFile` 을 동일하게 사용할 수 있습니다.

### MultipartFile 주요 메서드

`file.getOriginalFilename()` : 업로드 파일명

`file.transferTo(…)` : 파일 저장

실행

http://localhost:8080/spring/upload

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/886664ee-7d65-470e-899a-eed28994b263/Untitled.png)

실행 로그

```java
request=org.springframework.web.multipart.support.StandardMultipartHttpServletRequest@17934564
itemName=itemAAA
multipartFile=org.springframework.web.multipart.support.StandardMultipartHttpServletRequest$StandardMultipartFile@3345fc3e
파일 저장 fullPath=${file.dirUntitled-new.png
```