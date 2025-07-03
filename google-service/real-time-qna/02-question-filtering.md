# 질문 필터링하기
시스템과 제도는 악인의 시선으로 바라보라는 말이 있지요? 행사와 상관없는 비방성 글이나 부적절한 내용, 광고성 글이 제출될 수도 있습니다.  
인공지능이 질문이 올라오면 부적절한 지 여부를 확인하고 필터링을 하게 하는 방법도 있겠으나, 행사 주최측에서 직접 필터링을 통해서 부적절하거나 중복된 질문들을 처리할 수 있게 하는 과정을 추가하면 좋겠단 생각을 했습니다.  

## 구현 방법
이 기능을 구현하려면 각 이벤트를 만들 때마다 스프레드시트를 수동으로 변경하고, 코드를 한 번 업데이트해야 합니다.

### 1단계: 스프레드시트 업데이트 (각 이벤트마다 진행)
설문지가 처음 몇 개의 열을 직접 제어하므로, 이 새 열은 이벤트를 만들 때마다 수동으로 추가해야 합니다.

🚀 이벤트 관리 > 새 이벤트 만들기를 실행한 후, 새로 생성된 결과 시트(예: "3분기 타운홀 미팅 - 질문 모음")를 엽니다.

D1 셀에 열 제목으로 **승인**이라고 입력합니다.

D2 셀(새 제목 바로 아래 칸)을 클릭합니다.

키보드에서 Ctrl + Shift + ↓ (Mac의 경우 Cmd + Shift + ↓)를 누릅니다. 이렇게 하면 D2 셀부터 시트 맨 아래까지 모든 셀이 선택됩니다.

이제 메뉴에서 삽입 > 체크박스를 클릭합니다.

### 2단계: Apps Script 코드 업데이트
D열의 체크박스가 선택(true)된 행만 가져오도록 getMessages 함수를 수정해야 합니다.

Apps Script 프로젝트를 엽니다.

Code.gs 파일에서 기존 getMessages 함수를 아래 코드로 교체합니다.

```JavaScript

/**
 * '승인' 체크박스가 선택된 메시지만 특정 스프레드시트에서 가져옵니다.
 */
function getMessages(sheetId) {
  if (!sheetId) {
    return { status: "error", message: "오류: URL에 시트 ID가 없습니다.", data: [] };
  }

  try {
    const spreadsheet = SpreadsheetApp.openById(sheetId);
    const sheet = spreadsheet.getSheets()[0];
    const data = sheet.getDataRange().getValues();

    // ✨ 변경점: 4번째 열(인덱스 3)의 체크박스가 true인 행만 필터링합니다.
    const messages = data.slice(1)
      .filter(row => row[3] === true)
      .map(row => {
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
### 3단계: 저장 및 재배포
Code.gs 파일의 변경사항을 **저장(💾)**합니다.

핵심 데이터 조회 로직이 변경되었으므로, 반드시 웹 앱을 다시 배포해야 합니다. 배포 > 배포 관리로 이동하여 기존 배포를 수정한 후, 버전을 새 버전으로 선택하세요.
