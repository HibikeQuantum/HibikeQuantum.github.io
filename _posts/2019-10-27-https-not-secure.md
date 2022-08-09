---
layout: single
title: "HTTPS- Not Secure 디버깅"
date: 2019-10-27
---

  ![](/assets/img/1*fKvhRwG9jPeZNvemJNjIqw.png)알고나면 크게 어려운건 아닌데 어디서 봐야할지, 그것도 아니면 어떤 키워드로 검색해야할지 감이 안와서 조금 헤매서 정리하고자한다.

![](/assets/img/1*xe3UncPXPAodJ3ElqK_8ZQ.png)문제내 서비스는 Cloudfront에서 HTTPS로 서비스되고 있다. (인증서를 발급해서 등록했다) 그런데 서비스는 NOT Secure하다고 뜬다. IDE의 콘솔도 아니고 AWS에서 로그를 찍어 주는 것도 아니고 어디가면 이문제를 해결할 수 있을까?

정답은 개발자도구의 Security 탭이다.

![](/assets/img/1*h2w5LyeLdY4aEOPbzSxbSQ.png)이곳에서 SSL통신 규격을 점검해서 리포트해준다. 이런 서비스를 해주는 웹사이트들도 있지만 (전체 웹페이지를 봇으로 돌면서 점검해준다.) 개발자도구를 쓴다면 소규모에선 굳이 필요없어보인다.

![](/assets/img/1*Rx-qYiWvJC7CQf0eH9eGPQ.png)나같은 경우는 이런 에러였다. 분명 infra.duckhoo.site는 AWS에서 인증해준 인증서로 서비스 되고 있었는데 not trust다. 메시지를 읽어보니 Client에서 API서버를 DNS주소가 아니라 직접 IP로 호출하고 있는점이 문제였다. 그것을 invalid 증명으로 판단하고 있었다. 서명된 host와 실제 사용되는 host가 다르면 not trust하는듯하다. 클라이언트에서 DNS주소(infra.duckhoo.site)를 호출하도록 수정하니 문제해결.

  