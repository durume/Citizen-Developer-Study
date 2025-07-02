# 구글 시트와 설문지를 활용한 실시간 질문 수집 앱

이 프로젝트는 세미나, 포럼, 타운홀 미팅과 같은 행사에서 청중의 질문이나 메시지를 실시간으로 방송할 수 있는 오픈소스 서버리스 웹 애플리케이션입니다. 모든 기능은 여러분의 구글 계정 내에서 구글 Apps Script, 구글 설문지, 구글 시트를 통해 **무료**로 작동합니다.

## ✨ 주요 기능

  * **원클릭 이벤트 설정:** 단 한 번의 클릭으로 이벤트용 고유 구글 설문지와 결과 저장용 구글 시트가 생성됩니다.
  * **실시간 방송:** 제출된 질문이 별도의 새로고침 없이 웹페이지에 실시간으로 나타납니다.
  * **서버리스 및 무료:** 구글의 인프라에서 모든 것이 실행됩니다. 별도의 서버, 데이터베이스, 호스팅 비용이 필요 없습니다.
  * **쉬운 관리:** '제어판' 역할을 하는 중앙 스프레드시트에서 모든 이벤트, 설문지 링크, 방송 페이지 링크를 자동으로 관리합니다.
  * **자유로운 커스터마이징:** 코드가 공개되어 있어 디자인을 변경하거나 새로운 기능을 쉽게 추가할 수 있습니다.

-----

## 📌 수정 사항: `dev` 와 `exec` URL 문제 해결

이전 테스트에서 발견된 방송 페이지 링크가 가끔 `/dev` URL로 생성되는 문제는 구글 Apps Script의 작동 방식과 관련이 있습니다. 이 문제를 해결하고 항상 올바른 배포용(`/exec`) URL이 생성되도록, 스크립트 안에 여러분의 고유한 웹 앱 URL을 **상수**로 지정하는 방식을 사용합니다.

아래 `Code.gs` 파일의 `createNewEvent()` 함수는 이 상수를 사용하도록 업데이트되어, 항상 정확한 링크가 생성됩니다. 이 설정은 설치 과정 **5단계**에서 진행하게 됩니다.

-----

## ⚙️ 설치 및 설정 방법

아래 단계를 따라 여러분만의 실시간 Q\&A 앱을 만드세요.

### 1단계: 제어판 스프레드시트 만들기

1.  [구글 시트](https://sheets.google.com)로 이동하여 새 스프레드시트를 만듭니다.
2.  이 시트의 이름을 '이벤트 질문 제어판'과 같이 알아보기 쉽게 지정하세요. 이 시트가 여러분의 메인 대시보드가 됩니다
![image](https://github.com/user-attachments/assets/779fab13-bcdf-4360-8a3b-87fdd930ef88)  


### 2단계: Apps Script 편집기 열기

1.  새로 만든 스프레드시트의 상단 메뉴에서 **확장 프로그램 \> Apps Script**를 클릭합니다.  
![image](https://github.com/user-attachments/assets/63acb8d1-1021-4e16-ae1e-d8d2e7e3a366)  
2.  새 탭에서 Apps Script 편집기가 열립니다.
![image](https://github.com/user-attachments/assets/9272f9eb-801c-4dd3-a461-a11397c35782)
3.  Apps Script 편집기의 프로젝트 이름을 바꿉니다.
![image](https://github.com/user-attachments/assets/cac503d4-75db-4eeb-89d8-41735e435857)


### 3단계: 서버 측 코드 추가하기 (`Code.gs`)

1.편집기에 기본으로 생성되어 있는 `Code.gs` 파일의 **내용을 모두 삭제**합니다.  
2.  아래의 전체 코드 블록을 복사하여 비어있는 `Code.gs` 파일에 **붙여넣기** 하세요.

<!-- end list -->

```javascript
// 5단계에서 여러분의 고유한 웹 앱 URL로 교체할 상수입니다.
const WEB_APP_URL = "여기에_고유_웹_앱_URL을_붙여넣으세요";

/**
 * 스프레드시트가 열릴 때 커스텀 메뉴를 생성합니다.
 */
function onOpen() {
  SpreadsheetApp.getUi()
      .createMenu('🚀 이벤트 관리')
      .addItem('새 이벤트 만들기', 'createNewEvent')
      .addToUi();
  
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  if (sheet.getRange("A1").getValue() === "") {
    sheet.getRange("A1:D1").setValues([["이벤트 이름", "질문 제출 링크", "결과 시트 링크", "방송 페이지 링크"]]).setFontWeight("bold");
  }
}

/**
 * 새 이벤트를 위한 구글 설문지와 결과 시트를 생성합니다.
 */
function createNewEvent() {
  if (WEB_APP_URL === "여기에_고유_웹_앱_URL을_붙여넣으세요") {
    Browser.msgBox("설정이 필요합니다", "설치 안내 5단계를 진행하여 스크립트에 웹 앱 URL을 추가해주세요.", Browser.Buttons.OK);
    return;
  }

  const eventName = Browser.inputBox("새 이벤트의 이름을 입력하세요 (예: 2025년 3분기 타운홀 미팅)");
  if (eventName === 'cancel' || eventName === '') {
    Browser.msgBox("이벤트 생성이 취소되었습니다.");
    return;
  }
  
  const spreadsheet = SpreadsheetApp.create(eventName + " - 질문 모음");
  const form = FormApp.create(eventName);
  form.setDestination(FormApp.DestinationType.SPREADSHEET, spreadsheet.getId());
  form.setDescription("이름(또는 닉네임)과 질문이나 메시지를 남겨주세요.");
  form.addTextItem().setTitle("이름 (닉네임 가능)");
  form.addParagraphTextItem().setTitle("메시지 또는 질문").setRequired(true);

  const formUrl = form.getPublishedUrl();
  const sheetUrl = spreadsheet.getUrl();
  const broadcastUrl = WEB_APP_URL + '?sheetId=' + spreadsheet.getId();

  const controlPanelSheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  const newRow = controlPanelSheet.getLastRow() + 1;

  controlPanelSheet.getRange(newRow, 1).setValue(eventName);

  const formLink = SpreadsheetApp.newRichTextValue().setText("설문지 바로가기").setLinkUrl(formUrl).build();
  controlPanelSheet.getRange(newRow, 2).setRichTextValue(formLink);

  const sheetLink = SpreadsheetApp.newRichTextValue().setText("결과 시트 보기").setLinkUrl(sheetUrl).build();
  controlPanelSheet.getRange(newRow, 3).setRichTextValue(sheetLink);

  const broadcastLink = SpreadsheetApp.newRichTextValue().setText("방송 페이지 열기").setLinkUrl(broadcastUrl).build();
  controlPanelSheet.getRange(newRow, 4).setRichTextValue(broadcastLink);
  
  Browser.msgBox("이벤트가 성공적으로 생성되어 제어판에 기록되었습니다.");
}

/**
 * 메인 웹페이지를 서비스하고, URL의 sheetId를 HTML 파일에 전달합니다.
 */
function doGet(e) {
  const sheetId = e.parameter.sheetId;
  const htmlTemplate = HtmlService.createTemplateFromFile('index');
  htmlTemplate.sheetId = sheetId;
  return htmlTemplate.evaluate()
    .setTitle("실시간 질문")
    .addMetaTag('viewport', 'width=device-width, initial-scale=1');
}

/**
 * 특정 스프레드시트에서 메시지 목록을 가져옵니다. (진단 기능 포함)
 */
function getMessages(sheetId) {
  if (!sheetId) {
    return { status: "error", message: "오류: URL에 시트 ID가 없습니다.", data: [] };
  }
  
  try {
    const spreadsheet = SpreadsheetApp.openById(sheetId);
    const sheet = spreadsheet.getSheets()[0];
    const data = sheet.getDataRange().getValues();
    
    const messages = data.slice(1).map(row => {
      return {
        timestamp: new Date(row[0]).toLocaleString('ko-KR'),
        name: String(row[1]),
        message: String(row[2])
      };
    });

    return {
      status: "success",
      sheetName: sheet.getName(),
      totalRows: data.length,
      messageCount: messages.length,
      data: messages
    };
  } catch (e) {
    return {
      status: "error",
      message: "오류가 발생했습니다: " + e.message,
      data: []
    };
  }
}
```
3.  `Code.gs`에 붙여넣기 한 모습
![image](https://github.com/user-attachments/assets/fb303c8d-9874-4565-9f3c-4e0f79ba5c91)

### 4단계: 프론트엔드 코드 추가하기 (`index.html`)

1.  Apps Script 편집기에서 '파일' 옆의 **`+` 아이콘**을 클릭하고 **HTML**을 선택합니다.  
![image](https://github.com/user-attachments/assets/492ed710-40d2-4eb3-8d41-328ed19ad73a)

2.  새 파일의 이름을 `index`로 지정하고 Enter 키를 누릅니다.  
![image](https://github.com/user-attachments/assets/158029d4-1656-4bf0-97b1-0c4d6f1aa5af)

3.  생성된 `index.html` 파일의 **내용을 모두 삭제**합니다.  
4.  아래의 전체 코드 블록을 복사하여 비어있는 `index.html` 파일에 **붙여넣기** 하세요.

<!-- end list -->

```html
<!DOCTYPE html>
<html>
  <head>
    <base target="_top">
    <style>
      body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background-color: #f8f9fa; color: #212529; margin: 0; }
      .container { max-width: 800px; margin: 20px auto; padding: 0 20px; }
      h1 { text-align: center; color: #495057; }
      .card { background-color: white; border: 1px solid #dee2e6; border-radius: 8px; margin-bottom: 15px; padding: 15px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
      .card-header { font-size: 1.1em; font-weight: bold; color: #007bff; margin-bottom: 10px; }
      .card-body { font-size: 1em; line-height: 1.6; white-space: pre-wrap; word-wrap: break-word; }
      #loading { text-align: center; font-size: 1.2em; color: #6c757d; padding: 40px; }
    </style>
  </head>
  <body>
    <div class="container">
      <h1>실시간 질문</h1>
      <div id="messages-container">
        <p id="loading">메시지를 불러오는 중...</p>
      </div>
    </div>

    <script>
      const SHEET_ID = "<?!= sheetId ?>";
      
      setInterval(fetchMessages, 3000);
      document.addEventListener("DOMContentLoaded", fetchMessages);

      function fetchMessages() {
        if (!SHEET_ID) {
          handleResponse({ status: "error", message: "URL에 시트 ID가 없습니다." });
          return;
        }
        google.script.run
          .withSuccessHandler(handleResponse)
          .withFailureHandler(handleError)
          .getMessages(SHEET_ID);
      }

      function handleResponse(response) {
        const container = document.getElementById('messages-container');
        const loading = document.getElementById('loading');

        container.innerHTML = ''; 

        if (response.status === 'error') {
          loading.innerText = response.message;
          container.appendChild(loading);
          return;
        }
        
        const messages = response.data;

        if (messages.length === 0) {
          loading.innerText = "아직 제출된 질문이 없습니다.";
          container.appendChild(loading);
          return;
        }

        messages.reverse().forEach(msg => {
          const card = document.createElement('div');
          card.className = 'card';
          const header = document.createElement('div');
          header.className = 'card-header';
          header.textContent = msg.name;
          const body = document.createElement('div');
          body.className = 'card-body';
          body.textContent = msg.message;
          card.appendChild(header);
          card.appendChild(body);
          container.appendChild(card);
        });
      }
      
      function handleError(error) {
        // You can optionally show an alert or a simple message on error
        const container = document.getElementById('messages-container');
        container.innerHTML = '<p id="loading">오류가 발생했습니다. 페이지를 새로고침 해주세요.</p>';
      }
    </script>
  </body>
</html>
```
5.  `index.html` 파일에 본문 붙여넣고 저장한 예  
![image](https://github.com/user-attachments/assets/424c1038-c0c2-487b-91ae-a33fe6894865)


### 5단계: 첫 배포 및 설정 (매우 중요)

앱을 한 번 배포하여 고유 URL을 얻은 후, 그 URL을 다시 스크립트에 붙여넣어야 합니다.

1.  Apps Script 편집기 오른쪽 상단의 파란색 **배포** 버튼을 누르고 **새 배포**를 선택합니다.  
![image](https://github.com/user-attachments/assets/1d76ba83-d440-4450-843e-3ddda06a951e)

2.  '유형 선택' 옆의 **톱니바퀴 아이콘 ⚙️** 을 클릭하고 **웹 앱**을 선택합니다.  
![image](https://github.com/user-attachments/assets/bfaf3a0f-f639-4bcb-9f29-80724a5782a0)

3.  아래와 같이 설정을 채웁니다.
      * **설명:** `버전 1`
      * **웹 앱 실행 계정:** `나(your-email@gmail.com)`
      * **액세스 권한이 있는 사용자:** **모든 사용자**
4.  **배포** 버튼을 누릅니다.  
![image](https://github.com/user-attachments/assets/e07766b5-808c-4542-b776-c2b00ed6079f)

5.  '권한 승인' 창이 나타나면 **액세스 승인** 버튼을 누릅니다. 여러분의 구글 계정을 선택하고, 다음 화면에서 **고급**을 클릭한 후, **'(프로젝트 이름)'(으)로 이동(안전하지 않음)'** 링크를 클릭하세요.   
![image](https://github.com/user-attachments/assets/b5362137-dccd-4bb3-9fd4-1c63fcbc3479)
![image](https://github.com/user-attachments/assets/c6ded0c7-55cd-4c12-b223-de6438046906)
마지막으로 **허용** 버튼을 누릅니다.  
![image](https://github.com/user-attachments/assets/9ef67c27-4575-484e-9088-f76b46d0c0c3)

7.  배포가 완료되면 웹 앱 URL이 포함된 창이 나타납니다. 여기에 있는 **웹 앱 URL을 복사**하세요. URL은 `/exec`로 끝납니다.  
![image](https://github.com/user-attachments/assets/b31bab8d-3bd4-4ada-bd53-873fd2964a4f)

8.  다시 `Code.gs` 파일 편집 화면으로 돌아와 첫 번째 줄을 찾습니다.
    `const WEB_APP_URL = "여기에_고유_웹_앱_URL을_붙여넣으세요";`  
![image](https://github.com/user-attachments/assets/261f2793-e4b1-458c-9be3-45facf13d569)  

9.  `"여기에_고유_웹_앱_URL을_붙여넣으세요"` 부분을 **방금 복사한 URL로 교체**합니다.  
![image](https://github.com/user-attachments/assets/3f708da3-c5b3-4210-8025-ce145f193a64)  

10.  **프로젝트 저장(💾)** 아이콘을 클릭합니다. 이제 모든 설정이 끝났습니다\!

-----

## 🙋‍♀️ 사용 방법

1.  여러분의 '이벤트 질의 제어판' 스프레드시트를 엽니다. 기존 비어 있던 스프레드시트가 새롭게 변화된 부분이 보입니다.  
![image](https://github.com/user-attachments/assets/76402ed9-0c04-4f20-8426-7559715fffda)

2.  상단 메뉴의 **🚀 이벤트 관리 \> 새 이벤트 만들기**를 클릭합니다.  
![image](https://github.com/user-attachments/assets/7071cf44-4e85-4985-841a-337d8f47053c)

3.  이벤트 이름을 입력하면 시트에 새로운 행이 추가됩니다.  
![image](https://github.com/user-attachments/assets/f714aa69-fc78-4288-9562-3c098f9427fe)  
![image](https://github.com/user-attachments/assets/3e6ce171-6eb1-4031-90a0-6f57201d16e3)

4.  청중에게는 \*\*'질문 제출 링크'\*\*를 공유하세요.  
![image](https://github.com/user-attachments/assets/d9c532f9-7fd3-4696-ab93-3a2cd4e3c4a3)

5.  행사장의 스크린에는 \*\*'방송 페이지 링크'\*\*를 띄워두세요.  
![image](https://github.com/user-attachments/assets/3926adce-0743-46ad-8631-903ae1208e52)

6.  질문이 제출되면 해당 페이지에 실시간으로 나타납니다.  
![image](https://github.com/user-attachments/assets/9b63f73e-7e7a-400a-be3e-cf26aab34ac1)  
![image](https://github.com/user-attachments/assets/f07a5e79-ee3b-4468-9b34-7f20b4880dc7)

-----

## 🔧 문제 해결 (Troubleshooting)

문제가 발생했을 때 참고할 수 있는 몇 가지 해결 방법입니다.

  * **문제:** "설정이 필요합니다" 라는 오류 메시지가 표시됩니다.

      * **원인:** 설치 5단계를 완료하지 않았습니다.
      * **해결:** 5단계에 따라 앱을 배포하고, 발급된 고유 URL을 `Code.gs` 파일의 `WEB_APP_URL` 상수에 붙여넣으세요.

  * **문제:** 방송 페이지가 비어있거나 진단 바에 오류가 표시됩니다.

      * **원인:** 대부분 권한 또는 배포 문제입니다.
      * **해결:** 먼저 페이지 하단의 **진단 바**에 표시된 오류 메시지를 읽어보세요. 문제의 원인에 대한 힌트를 얻을 수 있습니다. 권한 문제로 의심되면, '새 이벤트 만들기'를 다시 시도하여 권한 요청 창이 뜨는지 확인하세요. 모든 방법이 실패하면 **5단계**를 다시 진행하여 앱을 새로 배포하고 `WEB_APP_URL`을 갱신하세요.

  * **문제:** 제어판의 링크가 작동하지 않거나 오류 페이지로 이동합니다.

      * **원인:** 배포 설정이 잘못되었을 수 있습니다.
      * **해결:** **5단계**를 매우 신중하게 다시 진행하세요. 올바른 `/exec` URL을 복사하여 `WEB_APP_URL` 상수에 정확히 붙여넣었는지 확인하세요. 그 후, **새로운 이벤트를 만들어 테스트**하세요. 기존에 만들어진 이벤트의 링크는 갱신되지 않습니다.
