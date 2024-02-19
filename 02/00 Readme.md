본 과정은 Power BI를 실무에 적용하기 위한 과정입니다.  
보통 엑셀을 많이 다뤄보셨다면, 이제는 Power BI를 활용하여 데이터를 시각화하고 분석하는 방법을 배워보겠습니다.

# Power BI vs Excel
## 1. Power BI가 Excel보다 뛰어난 점
- **관계형 데이터 모델링**: Power BI는 복잡한 데이터 관계를 다루기 쉽고 다차원 분석을 가능하게 합니다.
- **실시간 대시보드와 보고서**: Power BI는 실시간 업데이트가 가능한 대시보드와 보고서를 제공하여, 최신 데이터로 의사결정할 수 있습니다.
- **풍부한 시각화**: Power BI는 다양한 시각화 기능을 제공합니다.
- **고급 데이터분석**: Power BI는 R, Python과 같은 고급 분석 기능을 비롯해 DAX(Data Analysis Expressions)를 통해 복잡한 계산과 분석을 수행할 수 있습니다.
- **인터렉티브한 시각화**: Power BI는 사용자가 시각화를 클릭하거나 드래그하여 데이터를 필터링하고 상호작용할 수 있는 기능을 제공합니다.
- **통합과 협업**: Power BI는 마이크로소프트 서비스와 연동되어 있어, 팀원과 협업하고 보고서를 쉽게 공유하고 파워플랫폼과 상호연동이 용이합니다.

## 2. Excel 수식과 Power BI DAX 비교
Excel 수식과 Power BI의 데이터 분석 표현식(DAX)은 모두 계산과 데이터 분석에 사용되지만, 각각 다른 환경에 서비스하며 독특한 능력을 가지고 있습니다.  

### 환경
- **Excel 수식**: Excel 스프레드시트 내에서 사용되며, 개인이나 소규모 데이터 분석 및 보고에 적합합니다.
- **DAX**: Power BI 내의 데이터셋에서 계산을 생성하는 데 사용되며, 비즈니스 인텔리전스 응용 프로그램에 필요한 더 큰 데이터셋과 더 복잡한 데이터 모델링을 위해 설계되었습니다.

### 구문과 복잡성
- **Excel 수식**: 등호(`=`)로 시작하여 함수 이름과 인자를 사용하는 직관적인 구조입니다. Excel 수식은 다양한 사용자가 접근할 수 있도록 설계되었습니다.
- **DAX**: 개별 셀보다는 테이블과 컬럼을 다루는 데 중점을 두며, Excel에 없는 함수와 구조를 포함하여 고급 데이터 모델링 및 분석을 지원합니다.

### 예시 비교

#### 예시 1: 값 합산하기
- **Excel 수식**: `=SUM(A1:A10)`
- **DAX**: `Total Sales = SUM(Sales[Amount]))`

Excel에서는 직접 셀 범위의 합을 구합니다. DAX에서는 테이블의 컬럼(`매출[금액]`)을 합산합니다.  

#### 예시 2: 평균 계산하기
- **Excel 수식**: `=AVERAGE(B2:B100)`
- **DAX**: `Average Sales = AVERAGE(Sales[Amount])`

Excel은 직접 셀 범위를 사용하는 반면, DAX는 테이블 컬럼을 작업합니다.  

#### 예시 3: 조건 로직
- **Excel 수식**: `=IF(A1 > 100, "High", "Low")`
- **DAX**: `Sales Category = IF(SUM(Sales[Amount]) > 100, "High", "Low")`

Excel의 IF 함수는 셀 값을 직접 비교합니다. 반면에 DAX의 IF 함수는 대개 컬럼에 대한 집계(`SUM`을 예제에서 사용)를 수행한 후에 로직을 적용합니다.  

#### 예시 4: 날짜 다루기
- **Excel 수식**: `=YEAR(TODAY())`
- **DAX**: `Current Year = YEAR(NOW())`

Excel과 DAX 모두 날짜를 조작할 수 있지만, DAX는 시간 기간에 걸친 비교적 날짜 분석을 위한 더 강력한 시간 지능 함수를 제공합니다(예: 작년 동기).  

### 주요 차이점
- **데이터 맥락**: Excel 수식은 셀 단위로 작동하는 반면, DAX 함수는 전체 컬럼과 테이블에 걸쳐 작동하여 더 동적이고 관계적인 데이터 분석을 가능하게 합니다.
- **데이터 모델링**: DAX는 테이블 간의 관계와 같은 복잡한 데이터 모델링을 지원하지만, Excel에서 그렇게 직관적으로 다루지 않는다는 차이가 있습니다.
- **시간 지능**: DAX에는 연초부터 현재까지, 분기 초부터 현재까지, 작년 동기 대비 등의 계산을 수행하기 쉽게 만드는 시간 지능 함수가 내장되어 있습니다.

요약하자면, Excel 수식은 직관적인 셀 기반 계산에 아주 적합하고 널리 접근할 수 있는 반면, DAX는 관계형 데이터 및 시간 기반 계산을 다룰 때 더 강력하고 정교한 도구를 제공합니다. 

### Excel과 구분하여 Power BI에서 데이터를 다루기 위한 주의사항
1. **데이터 모델링**: Power BI에서는 데이터 모델링이 중요합니다. 데이터 모델링을 통해 데이터를 관계형으로 연결하고, 필요한 데이터를 추출하여 분석할 수 있습니다. Excel을 계산기 또는 보고서를 사용하면서 흔히 하는 실수는 데이터 모델링을 무시하는 것입니다.
2. **테이블(표)을 DB로 인식**: Excel을 오랫동안 사용하면 Excel을 데이터를 관리하기 위한 툴이라기보다 숫자를 입력하고 계산하고 때론 보고서를 만드는 툴로 인식할 수 있습니다. Power BI는 데이터를 테이블로 관리하고, 테이블 간의 관계를 구축하여 데이터를 분석합니다. DB로서 테이블을 생각하고, 테이블 간의 관계를 구축하는 것이 중요합니다.

### 참고자료
- **DAX 시간 지능 함수**
  - [한글자료 보러가기](https://learn.microsoft.com/ko-kr/dax/time-intelligence-functions-dax?WT.mc_id=DX-MVP-5005254)
  - [영문자료 보러가기](https://learn.microsoft.com/en-us/dax/time-intelligence-functions-dax?WT.mc_id=DX-MVP-5005254)
- **Excel 및 Power BI를 사용한 최신분석 소개**:
  - [한글자료 보러가기](https://learn.microsoft.com/ko-kr/training/modules/modern-analytics-intro/?WT.mc_id=DX-MVP-5005254)
  - [영문자료 보러가기](https://learn.microsoft.com/en-us/training/modules/modern-analytics-intro/?WT.mc_id=DX-MVP-5005254)
- **Power BI와 Excel을 함께 사용하여 데이터 분석 초월**:
  - [한글자료 보러가기](https://learn.microsoft.com/ko-kr/training/modules/modern-analytics-excel/?WT.mc_id=DX-MVP-5005254)
  - [영문자료 보러가기](https://learn.microsoft.com/en-us/training/modules/modern-analytics-excel/?WT.mc_id=DX-MVP-5005254)
