# 국가관세종합정보망 서비스(UNI-PASS) 활용하기

## 1. 국가관세종합정보망 서비스란?
국가 관세 종합 정보망 서비스는 한국 관세청에서 제공하는 통합된 관세 정보 시스템입니다. 이 서비스는 UNI-PASS라고도 불리며, 한국 관세청에서 사용되며 많은 국가에서 기준으로 삼고 있습니다. UNI-PASS는 사용하기 쉽고 유지보수가 간편한 강력하고 포괄적인 관세 솔루션입니다. 관세 정보, 수출입 관리, 통관 고유 부호 조회, 법령 정보, 유통 업체 이력 신고 등 다양한 기능을 제공합니다.


## 2. UNI-PASS 사용하기

### 2.1. UNI-PASS 회원가입
회원가입은 [유니패스 웹사이트](https://unipass.customs.go.kr/csp/index.do)에서 진행할 수 있습니다. 회원가입을 하고 로그인을 하면, API 사용신청을 할 수 있습니다. 

### 2.2. API 사용신청 및 키 발급
무사히(?) 회원가입을 마치면 아래 이미지와 같이 오른쪽 위에 위치한 'My메뉴'에서 API를 사용신청합니다.  
![UNI-PASS OpenAPI 신청을 위해 회원가입 후 마이페이지로 이동](images/unipass-OpenAPI-01.png)

다음으로 'My메뉴'에서 왼쪽 메뉴 가운데 '서비스관리'의 오른쪽에 있는 확장표시를 눌러 'OpenAPI 사용관리'를 선택합니다.  
![UNI-PASS My메뉴에서 OpenAPI 사용관리 선택하기](images/unipass-OpenAPI-02.png)

'OpenAPI 사용관리'로 이동해서 필요한 내용들을 선택한 후에 오른쪽 아래에 있는 'OPEN API 신청'을 눌러서 신청을 마무리 하면 됩니다.  
![UNI-PASS OpenAPI 사용관리에서 OPEN API 신청하기](images/unipass-OpenAPI-03.png)

승인된 OpenAPI를 사용하려면 해당 API를 눌러서 아래의 상세내역에서 API 인증키를 확인할 수 있습니다.  
![UNI-PASS OpenAPI 사용관리에서 API 키 확인하기](images/unipass-OpenAPI-04.png)