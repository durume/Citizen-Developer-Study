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

## IoT and SharePoint List Integration with Raspberry Pi and DHT11 Sensor

This guide will walk you through setting up an IoT project that uses a Raspberry Pi to read data from a DHT11 sensor (temperature and humidity) and saves the data to a SharePoint list. We will use Microsoft Graph API for data integration and Power Automate for notifications.

### Prerequisites

- Raspberry Pi with Raspbian OS installed
- DHT11 Sensor
- SharePoint Online account
- Azure Active Directory (Azure AD) account
- Microsoft 365 account
- Python installed on Raspberry Pi
- Libraries: `adafruit-circuitpython-dht`, `requests`, `schedule`, `msal`

### Step 1: Set Up the Hardware

1. **Connect the DHT11 Sensor to Raspberry Pi**:
   - Connect the VCC pin of DHT11 to 3.3V pin on the Raspberry Pi.
   - Connect the GND pin of DHT11 to GND on the Raspberry Pi.
   - Connect the Data pin of DHT11 to GPIO pin 4 on the Raspberry Pi.

### Step 2: Install Required Libraries

```bash
pip install adafruit-circuitpython-dht
pip install requests
pip install schedule
pip install msal
```
### Step 3: Entra Admin Center에서 앱 등록
1. [Entra Admin Center](https://entra.microsoft.com)으로 이동해서 왼쪽 메뉴에서 앱 등록(Applications>App registrations)
3. 새 등록(New registration)을 클릭.
4. 필수 사항 채워넣기:
- Name: 원하는 앱 이름 입력(예를 들어 'IoT SharePoint Integration')
- Supported account types: Accounts in this organizational directory only (Single tenant)
- 
5. 등록(Register) 클릭.
6. Certificates & secrets > New client secret 한 다음 create a new secret. 해당 Value를 꼭 복사해서 저장해두어야 합니다. 생성할 때 보여주고 다음에는 안 보여줍니다.
7. API permissions > Add a permission > Microsoft Graph > Application permissions 순으로 처리합니다.
8. Add the permissions을 처리할 때 :
- Sites.ReadWrite.All
9. 마무리 한 다음 생성된 permission에 대해서 Grant admin consent for the permissions도 마무리 해주세요.

### Step 4: Configure the Application
config.py 라는 파일을 생성해서 Entra에 등록된 앱에 관한 정보를 각각 입력한 다음 해당 파일을 저장합니다:
```bash
# config.py

CLIENT_ID = "your-client-id"  # 등록한 앱의 애플리케이션(클라이언트) ID
CLIENT_SECRET = "your-client-secret-value"  # 앱을 등록할 때 추가한 Secret Value(생성할 때만 보여주므로 저장 필수)
TENANT_ID = "your-tenant-id"  # 등록한 앱의 Tenant ID 정보 (Overview에서 확인 가능)
AUTHORITY = f'https://login.microsoftonline.com/{TENANT_ID}'
SCOPE = ["https://graph.microsoft.com/.default"]
```
