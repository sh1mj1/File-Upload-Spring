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