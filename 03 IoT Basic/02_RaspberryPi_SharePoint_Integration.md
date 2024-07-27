# Raspberry Pi와 SharePoint List 통합하기
## 1. SharePoint List란?
SharePoint List는 Microsoft SharePoint에서 제공하는 데이터 저장 및 관리를 위한 서비스입니다. List는 테이블 형식으로 구성되어 있으며, 각각의 열은 필드로서 데이터를 저장합니다. 이를 통해 사용자는 다양한 유형의 데이터를 구성하고 관리할 수 있습니다.  

SharePoint List의 활용은 다양합니다. 몇 가지 예시로는:
- 문서 라이브러리: 문서를 업로드하고 버전 관리, 공유 및 액세스 제어를 할 수 있습니다.
- 작업 관리: 작업 목록을 생성하여 팀원 간의 작업 할당, 진행 상황 추적 및 우선순위 설정이 가능합니다.
- 문제 추적: 문제 목록을 생성하여 문제 보고, 해결 상태 추적 및 팀 간 협업이 가능합니다.

SharePoint List는 사용자가 비즈니스 프로세스를 자동화하고 효율적으로 관리할 수 있는 강력한 도구입니다.

## 2. SharePoint List를 데이터베이스로 활용하는 까닭?

SharePoint List를 데이터베이스로 활용하는 이유는 여러 장점이 있습니다. 

첫째로, Raspberry Pi를 통해 DHT11 센서로부터 수집한 데이터를 SharePoint List에 저장하는 것은 간이성을 제공합니다. SharePoint List는 테이블 형식으로 구성되어 있어 데이터를 쉽게 구성하고 관리할 수 있습니다. 따라서, 센서로부터 수집한 데이터를 간단하게 필드에 저장하여 효율적으로 관리할 수 있습니다.

둘째로, SharePoint List는 Power Platform과의 손쉬운 연동을 가능하게 합니다. Power Platform은 Microsoft의 비즈니스 앱 플랫폼으로, 간단한 앱 및 워크플로우를 만들고 자동화할 수 있습니다. Raspberry Pi로부터 수집한 데이터를 SharePoint List에 저장하면, Power Platform을 사용하여 이 데이터를 활용하여 다양한 앱 및 워크플로우를 개발할 수 있습니다. 이를 통해 비즈니스 프로세스를 자동화하고 효율적으로 관리할 수 있습니다.

따라서, 우리는 이번에 손쉬운 데모를 위해서 Raspberry Pi를 통해 수집한 데이터를 간이성과 Power Platform과의 손쉬운 연동 등을 이유로 SharePoint List에 저장하기로 하겠습니다.

다만, 실무에서 활욯할 때에는 SharePoint의 속도, 보안 등의 취약점도 고려할 필요가 있습니다.

## 3. SharePoint List 활용
### 1. SharePoint List 만들기
