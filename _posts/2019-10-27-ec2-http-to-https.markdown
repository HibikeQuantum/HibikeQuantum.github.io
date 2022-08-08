---
layout:	single
title: "EC2 HTTP 웹서버를 HTTPS로 바꾸기"
date: 2019-10-27
toc: true
category: WEB
tags: [AWS, EC2, HTTP, PaaS]
excerpt: 
---

![](/assets/img/https.jpeg){:.mimg}
# EC2 HTTP 웹서버를 HTTPS로 바꾸기
## 개요
현재 HTTP로 서비스 되고 있는 내 플라스크 서버를 SSL을 적용한 HTTPS 서비스로 바꾸고 적용하는 과정을 정리했다. 

### 글쓴이의 사용 환경
EC2(linux AMI), EIP, Route53, 정식도메인, cert-bot, S3 호스팅, Cloud Front

### 절차
1. 무료 인증서 프로젝트인 `Letsencript`는 정식도메인에서 서비스되는 서버에만 인증서를 발급한다.
2. 현재 가비아로부터 duckhoo.site를 구매해서 Route53을 이용해 도메인서버를 운영하고 있다. 
3. `infra.duckhoo.site` 의 이름으로 현재 elastic IP로 서비스되고 있는 EC2서버에 정식도메인을 부여 
4. HTTPS로 서버가 운영되고 
5. 최종적으로 duckhoo.site 클라이언트가 HTTPS 서비스를 지원하는게 최종적 목적지다. (꼭 HTTPS를 해야하는건 아니다. ELB를 활용하면 VPC단에서 HTTPS서비스를 제공할 수 있다.)

---

## Route53 설정
![](/assets/img/route53config.jpg)
{:.align-center}
route53 console
{: .captionC}

Route53에서 소유중인 도메인의 하위 도메인으로 `infra` 레코드 세트를 만들었었다. 아래에 입력되는 아이피는 탄력적아이피이며 (Elastic IP) 는 AWS 프리티어에 1개 제공된다.

![](/assets/img/RouteHTTPS_1.png)
{:.align-center}
local terminal, 만든 레코드에 대한 통신확인 완료
{: .captionC}

## CERT 발급
1. SSH EC2 접속  
2. certbot-auto 다운로드 및 실행  
`$ wget  https://dl.eff.org/certbot-auto$` 
`$ chmod a+x certbot-auto`
3. 오토봇을 실행한다. 마지막에 정식도메인을 입력해줘야한다.  
`$ sudo ./certbot-auto —debug -v —server https://acme-v01.api.letsencrypt.org/directory certonly -d infra.duckhoo.site`
4. 옵션을 다 안지정해줘서 정보를 2개 물어본다. 첫번째는 email
두번째는 서비스할 webroot다. flask 내장서버를 쓰고 있어서 뭘 입력해줘야하나 고민하다가 프로젝트 폴더에 cert폴더를 만들어서 입력했다.  
`Input the webroot for infra.duckhoo.site: (Enter ‘c’ to cancel): /home/ec2-user/DuckhooGosa-server/cert/`
5. 인증서가 발급된 모습

---

## Server Side HTTPS
이제 인증서를 들고 서비스를 하도록 코드를 짤 차례다. SSL모듈을 사용하려고 pip install ssl 을 하니 `SyntaxError: Missing parentheses in call to ‘print’. Did you mean print(‘looking for’, f)?` 이런 에러가 뜨는데, 알아보니 현재 내 flask는 내장웹서버를 쓰기 때문에 별도의 설치가 필요 없었다. (권장 사항은 아니다 나중에 WSGI 를 붙일예정. 파이선 웹서버를 프로덕션 레벨에서 쓸때는 내장 WSGI를 쓰면 안된다.)  
개발환경에서 먼저 확인을 해보고 싶어서 로컬부터 openssl로 만든 pem으로 서비스해보기로 했다.  
```
$ pip install pyopenssl
$ openssl req -x509 -newkey rsa:4096 -nodes -out cert.pem -keyout key.pem -days 36500
```
이렇게하면 100년 유효기간의 키가 생겼다. 제3자의 증명을 위한 cert 지만 이렇게 만드는건 자기 스스로가 증명하고 자기가 사용하는 꼴이라  브라우저에서 접근하면 경고창이 보이지만 (브라우저는 인정하는 CA 인증과정을 거친 CERT만을 보증하도록 로직을 갖추고 있다) 어쨌든 일단 형식은 갖추었다.

```python
if __name__ == '__main__':
    pemTuple = (app.config['CERT_PEM'], app.config['PRIVATE_KEY_PEM'])
    app.run(port=app.config['PORT'], host=app.config['SERVER_HOST'], ssl_context=pemTuple)
```
{:.align-center}
app.py
{: .captionC}

 flask-app에 인증서를 등록시킨다. `app.config`의 내용은 각자 프로젝트 환경변수 설정방법에 따라 다르므로 자세한 설명은 생략한다.

개발환경의 API서버는 이제 https 서비스를 시작했다. CRA의 내장서버도 옵션을 켜면 자기가 사인한 허술한 인증서로 https 서비스로 시작된다.[🔗참고 링크: CRA using-https-in-development](https://create-react-app.dev/docs/using-https-in-development/#!)  

---
한쪽만 Https가 되면 Mixed Contents 에러가 난다. 개발환경에서 충분히 준비가 되었는지 확인하고 이제 배포 테스트용 클라이언트, 배포 테스트 서버 브랜치를 업데이트시킨다.  

실행을 시켜보니 에러가 뜬다.
```
File “/usr/lib64/python3.6/ssl.py”, line 750, in __init__self._context.load_cert_chain(certfile, keyfile)PermissionError: [Errno 13] Permission denied
``` 
넘겨준 인증서를 읽는데 실패한 에러다. 인증서 경로를 가면 경로의 파일은 넘겨받은 파일은 Soft 카피(Soft-Link)다. 원본과 복사본에 실행권한을 주면 된다. 이때 `chmod 777` 같은 every 레벨로 줘도 동작은 하지만 보안적으로 권장하지는 않는다. Server를 실행할 유저 및 그룹을 만들어서 그 그룹한테 1(+x)의 권한만 주는 방법을 권장하고 있다.
  
[🔗 참고자료: Let’s encrypt SSL couldn’t start by “Error: EACCES: permission denied, open…](https://stackoverflow.com/questions/48078083/lets-encrypt-ssl-couldnt-start-by-error-eacces-permission-denied-open-et)  
[🔗 참고자료: lets-encrypt-ssl-couldnt-start-by-error-eacces-permission-denied-open-et](https://stackoverflow.com/questions/48078083/lets-encrypt-ssl-couldnt-start-by-error-eacces-permission-denied-open-et)  
---

## Client Side HTTPS
이제 서버를 동작시켜보니 서명을 기반으로 통신은 잘되는데 크롬에서는 NOT Secure 경고를 하고 있다.  

![](/assets/img/1*xe3UncPXPAodJ3ElqK_8ZQ.jpg)

원인을 찾아보니 버킷이름 SSL 프로토콜 약속을 위반한것 같다. 다음과 같은 내용을 찾았다. 
> Secure Sockets Layer(SSL)와 함께 가상 호스팅 방식의 버킷을 사용할 경우, **SSL 와일드카드 인증서는 마침표가 포함되지 않은 버킷에만 연결됩니다.** 이 문제를 해결하려면 HTTP를 사용하거나, 인증서 확인 로직을 직접 작성해야 합니다. 가상 호스팅 방식의 버킷을 사용할 경우, 버킷 이름에 마침표(“.”)를 사용하지 않는 것이 좋다.

버킷이름을 `test.duckhoo.site` 로 설정해두었는데 SSL 프로토콜에 위배된것같다. 버킷이름을 바꿀 방법은 없을까. 찾아봤는데 `AWS CLI`로 버킷을 복사하고 동기화하는 방법만 보이길래 새로 만들었다.

![](/assets/img/1*1oF0AcTYc_j0tdwjlZxiFQ.jpg){:.simg}
다시 CloudFront 설정을했다.
{: .caption}..

오리진 도메인만 새로운 버킷으로 고쳐주면 Distribution을 새로 만드는 번거로움을 피할 수 있다. 중간에 SSL통신을 크롬이 인증서를 invalid로 판단해서 Not Secure를 보여주는 문제가 있었는데 이걸 해결한 우여곡절을 확인하고 싶다면 
[🔗 다른 포스트 링크](https://medium.com/kangtaehun-io-devtory/https-not-secure-%EB%94%94%EB%B2%84%EA%B9%85-dc32576e075a) 

SSL은 클라이언트나 서버 혼자 하는게 아니므로 어느 쪽이든 클라이언트와 서버가 요구하는 원칙을 위배한 통신을 하면 Not secure가 뜨게되므로 주의.

![](/assets/img/mySite.jpg){:.ming}
{: .caption}

검증된 오리진(제3자가 인정한 인증서를 가진 호스트)들이 검증된 통신(SSL)을 지키고 있으면 크롬이 Secure 하다고 인정한다. SSL을 사용하면 통신의 오버헤드는 미미하게 증가 하지만 암호화된 통신을 구현할 수 있다. 자세한 원리는 `비대칭 공개키`를 검색.