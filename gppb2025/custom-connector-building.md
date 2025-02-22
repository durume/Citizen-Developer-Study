# Power Apps 커스텀 커넥터 생성 가이드

> **목표**  
> 이 문서는 직접 만든 Flask 웹 API(예: `/orders`, `/orders/<id>` 등)를 **Power Apps**에서 커스텀 커넥터로 설정하고 활용하는 방법을 단계별로 안내합니다.  
> 여기서는 실제 배포 대신 **VS Code Dev Tunnels**로 만든 임시 URL `https://c3r6cfbq-5000.asse.devtunnels.ms/`을 사용해 Flask 앱에 접속합니다.  

---

## 1. 사전 준비 사항

1. **Flask 앱 확인**  
   - Flask 코드가 로컬에서 정상 동작하는지, 그리고 `GET /orders` 등 필요한 라우트를 가진 상태인지 확인하세요.  
   - 예시 라우트:
     ```python
     @app.route('/orders', methods=['GET'])
     def get_orders():
         return jsonify([
             { "id": 1, "fruit": "Apple", "quantity": 10, "date": "2025-01-01", "amount": 20.0 },
             { "id": 2, "fruit": "Banana", "quantity": 5,  "date": "2025-01-02", "amount": 15.0 }
             # ...
         ])
     ```
2. **Dev Tunnels 주소 확인**  
   - VS Code에서 Dev Tunnels(포트 포워딩)를 설정한 뒤, 다음과 같은 임시 URL을 발급받았다고 가정합니다:  
     ```
     https://c3r6cfbq-5000.asse.devtunnels.ms/
     ```
   - 이 주소가 외부에서 접근 가능한지 브라우저를 통해 `/orders` 엔드포인트를 테스트해보세요:  
     ```
     https://c3r6cfbq-5000.asse.devtunnels.ms/orders
     ```
   - 응답으로 JSON 배열이 정상 반환되어야 커스텀 커넥터가 문제없이 동작합니다.

---

## 2. Power Apps 환경 접속

1. **Power Apps 포털 열기**  
   - [https://make.powerapps.com](https://make.powerapps.com) 에 접속  
   - 적절한 **환경(Environment)** 선택 (기본값: “Default” 등)

2. **커스텀 커넥터** 메뉴 이동  
   - 왼쪽 메뉴에서 **데이터** → **커스텀 커넥터** 또는 **Dataverse** → **커스텀 커넥터**를 찾으세요.  
   - 화면 오른쪽 상단에서 **새 커스텀 커넥터(New custom connector)** → **새로 만들기(블랭크에서 시작)** 선택

---

## 3. 커스텀 커넥터 초기 설정

1. **이름 지정**  
   - 예: `FlaskOrdersConnector`  
2. **아이콘, 배경색, 설명** 등 기본 정보 입력  
3. **호스트(Host) / 기본 URL(Base URL)**  
   - **Host**: `c3r6cfbq-5000.asse.devtunnels.ms` (프로토콜 `https://` 제외)  
   - **Base URL**: `/` (Flask 엔드포인트가 루트에 있다고 가정)  
4. **보안(Security)**  
   - 테스트용으로 인증이 불필요하다면 **No authentication**(인증 안 함)을 선택  
   - 실제 운영환경에서는 API Key나 OAuth 적용을 고려

---

## 4. 엔드포인트 정의 (Definition)

커스텀 커넥터에서 가장 중요한 단계입니다. Flask에서 제공하는 각 엔드포인트별로 **동작(Operations)** 을 추가해야 합니다.

### 4.1 예시: GET `/orders`

1. **+ New action** 클릭  
2. **Summary**: “Get all orders” (간단 설명)  
3. **Description**: “전체 과일 주문 목록 조회” (상세 설명)  
4. **Operation ID**: “GetAllOrders” (스크립트나 Power Fx에서 사용될 고유 ID)  
5. **Request**  
   - **Verb**: `GET`  
   - **URL**: `/orders` (Base URL이 `/`이므로 최종 호출 경로는 `https://c3r6cfbq-5000.asse.devtunnels.ms/orders`)  
6. **Responses**  
   - 상태 코드 `200` OK를 가정  
   - **Body**: 배열 형태로 정의  
     ```json
     [
       {
         "id": 1,
         "fruit": "Apple",
         "quantity": 10,
         "date": "2025-01-01",
         "amount": 20.0
       }
     ]
     ```
   - 커넥터 편집기의 **Import from sample** 기능을 이용해 JSON 샘플을 붙여넣으면 자동으로 스키마가 생성됩니다.

### 4.2 추가 엔드포인트 정의

- `GET /orders/{order_id}`
- `GET /sales?date=YYYY-MM-DD`
- 등등

동일한 절차로 **Operation ID**, **Verb**, **URL**, **응답(Responses)** 를 설정해줍니다.

---

## 5. 커넥터 저장 및 테스트

1. **저장(Save)**  
   - 오른쪽 상단 **Update connector** or **Save connector** 버튼 클릭  
2. **테스트(Test)** 탭 이동  
   - **New connection** 생성 (No auth라면 이름만 입력)  
   - 커넥터가 표시되는지 확인 후, **Test operation** 버튼으로 실제 호출  
   - 정상적으로 200 OK, JSON 응답이 나오면 성공

> **주의**: 404 오류가 발생한다면, Flask 코드에 `/orders` 라우트가 있는지, Dev Tunnels URL이 제대로 설정되었는지, 혹은 Base URL/Path 부분이 올바른지 다시 확인하세요.

---

## 6. Power Apps에서 커넥터 사용하기

1. **캔버스 앱(Canvas App)** 만들기  
   - **새로 만들기** → **빈 캔버스 앱**  
2. **데이터 추가**  
   - 왼쪽 **데이터(Data)** 메뉴 → **+ 데이터 추가** → **커스텀** → 앞서 만든 `FlaskOrdersConnector` 선택  
3. **커넥터 호출**  
   - 예: **갤러리(Gallery)** 추가 후, `Items` 속성에  
     ```powerfx
     FlaskOrdersConnector.GetAllOrders()
     ```
   - Connector가 반환한 주문 목록을 갤러리에 바인딩하여 화면에 표시 가능

---

## 7. 결론 & 추가 팁

- 이 가이드는 **임시 Dev Tunnels** 주소를 활용해 로컬 Flask 앱을 Power Apps 커넥터로 연결하는 과정을 설명했습니다.  
- 실제 운영환경에서는 Flask 앱을 Azure, Heroku, AWS 등 클라우드에 배포한 뒤, **공인(public) URL**을 커넥터에 등록하는 방식을 권장합니다.  
- **인증**이 필요한 경우, API Key나 OAuth 2.0 등의 방식을 커넥터에 설정하여 보안을 강화할 수 있습니다.  
- 커넥터 정의(Definition)에서 다양한 응답 코드(200, 404, 400 등)에 대한 스키마를 구성하면, Power Apps나 Power Automate에서 오류 처리 로직을 더 정확하게 구성할 수 있습니다.

---

### 참고 링크

- [Flask 공식 문서](https://flask.palletsprojects.com/)  
- [VS Code Tunnels 소개](https://code.visualstudio.com/docs/remote/tunnels)  
- [Power Apps 커스텀 커넥터 개요](https://learn.microsoft.com/ko-kr/powerapps/maker/canvas-apps/register-custom-api?tabs=custom-connector)  
- [Power Automate 커스텀 커넥터 가이드](https://learn.microsoft.com/ko-kr/connectors/custom-connectors/)

위 단계를 모두 따르면, 로컬에서 구동 중인 Flask 애플리케이션을 Power Apps(또는 Power Automate, Power Virtual Agents)에서 바로 호출할 수 있는 **커스텀 커넥터**로 활용할 수 있습니다.  
