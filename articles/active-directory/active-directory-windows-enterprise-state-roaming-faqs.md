<properties
	pageTitle="설정 및 데이터 로밍 FAQ | Microsoft Azure"
	description="설정 및 앱 데이터 동기화에 대한 IT 관리자의 질문에 답변합니다."
	services="active-directory"
    keywords="엔터프라이즈 상태 로밍 설정, windows 클라우드, 엔터프라이즈 상태 로밍에 대한 질문과 대답"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="femila"/>

# 설정 및 데이터 로밍 FAQ

이 토픽에서는 설정 및 앱 데이터 동기화에 대한 IT 관리자의 질문에 답변합니다.

## 어떤 데이터가 로밍됩니까?
**Windows 설정**: Windows 운영 체제에 내장된 PC 설정. 일반적으로 사용자의 PC를 개인 설정하는 설정이며, 다음과 같은 광범위한 범주를 포함합니다.

- **테마**: 바탕 화면 테마, 작업 표시줄 설정 등
- **Internet Explorer 설정**: 최근에 열어본 탭, 즐겨찾기 등
- **Edge 브라우저 설정**: 즐겨찾기, 읽기 목록
- **암호**: 인터넷 암호, Wi-Fi 프로필 등
- **언어 기본 설정**: 키보드 레이아웃, 시스템 언어, 날짜 및 시간 등
- **간편한 액세스**: 고대비 테마, 내레이터, 돋보기 등
- **기타 Windows 설정**: 명령 프롬프트 설정, 응용 프로그램 목록 등

**응용 프로그램 데이터**: 범용 Windows 앱은 “로밍” 폴더에 설정 데이터를 쓸 수 있으며, 이 폴더에 쓰여진 모든 데이터는 자동으로 동기화됩니다. 이 기능을 활용하는 앱 디자인 여부는 개별 앱 개발자의 결정에 달렸습니다. 로밍을 사용하는 범용 Windows 앱을 개발하는 방법에 대한 자세한 내용은 [앱 데이터 저장소 API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) 및 [Windows 8 앱 데이터 로밍 개발자 블로그](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)를 참조하세요.

## 설정 동기화에 어떤 계정이 사용됩니까?
Windows 8 및 Windows 8.1에서는 설정 동기화에 항상 소비자 Microsoft 계정이 사용되었습니다. 엔터프라이즈 사용자가 Microsoft 계정을 Active Directory 도메인 계정에 연결하여 설정 동기화에 대한 액세스 권한을 얻을 수 있었습니다. Windows 10에서는 이 “연결된 Microsoft 계정” 기능이 기본/보조 계정 프레임워크로 대체됩니다.

기본 계정이란 Windows에 로그인하는 데 사용되는 계정으로 Microsoft 계정, Azure Active Directory(Azure AD) 계정, 온-프레미스 Active Directory 계정 또는 로컬 계정일 수 있습니다. 기본 계정 외에도 Windows 10 사용자는 장치에 하나 이상의 보조 클라우드 계정을 추가할 수 있습니다. 이러한 보조 계정은 일반적으로 Microsoft 계정, Azure Active Directory 계정 또는 Gmail이나 Facebook과 같은 기타 계정입니다. 이러한 보조 계정은 Single Sign-On, Windows 스토어 등의 추가 서비스에 대한 액세스를 제공하지만 설정 동기화는 지원하지 않습니다.

Windows 10에서 설정 동기화에는 장치에 대한 기본 계정만을 사용할 수 있습니다([Windows 8의 Microsoft 계정 설정 동기화에서 Windows 10의 Azure AD 설정 동기화로 업그레이드하려면 어떻게 해야 하나요?]를 참조(active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?)).

데이터는 장치의 서로 다른 사용자 계정 간에 절대로 혼합되지 않습니다. 설정 동기화에는 두 가지 규칙이 있습니다.
- Windows 설정은 항상 기본 계정으로 로밍됩니다.
- 앱 데이터는 앱을 획득하는 데 사용되는 계정으로 태그를 지정합니다. 기본 계정을 사용하여 태그가 지정된 앱만 동기화 됩니다. 앱 소유권 태그 지정은 Windows 스토어를 통해 앱이 사이드 로드될 때 또는 MDM(모바일 장치 관리)을 통해 앱이 사이드 로드될 때 결정됩니다.

앱 소유자를 식별할 수 없는 경우 기본 계정을 사용하여 로밍합니다. 장치를 Windows 8 또는 Windows 8.1에서 Windows 10으로 업그레이드하는 경우 모든 앱이 Microsoft 계정을 통해 획득한 것으로 태그가 지정됩니다. 왜냐하면 일반적으로 대부분의 앱은 Windows 스토어를 통해 획득되고, Windows 10 미만에서는 Azure AD에 Windows 스토어가 지원되지 않기 때문입니다. 오프라인 라이선스를 통해 앱을 설치하는 경우에는 해당 장치의 기본 계정을 사용하여 앱의 태그가 지정됩니다.

>[AZURE.NOTE]  
엔터프라이즈가 소유하고 Azure AD에 연결되어 있는 Windows 10 장치는 Microsoft 계정을 도메인 계정에 연결할 수 없습니다. Microsoft 계정을 도메인 계정에 연결하고 모든 사용자 데이터를 Microsoft 계정과 동기화(예: “연결된 Microsoft 계정 및 Active Directory” 기능을 통해 Microsoft 계정 로밍)하는 기능은 연결된 Active Directory/Azure AD 환경에 조인되는 Windows 10 장치에서 제거되었습니다.

## Windows 8의 Microsoft 계정 설정 동기화에서 Windows 10의 Azure AD 설정 동기화로 업그레이드하려면 어떻게 해야 하나요?
Microsoft 계정이 연결된 Windows 8 또는 Windows 8.1을 실행하는 Active Directory 도메인에 조인된 사용자는 자신의 Microsoft 계정을 통해 설정을 동기화합니다. Windows 10으로 업그레이드가 완료되면 도메인에 조인된 사용자는 Active Directory 도메인이 Azure AD와 연결되지 않는 한 계속해서 Microsoft 계정을 통해 사용자 설정을 동기화하게 됩니다. 온-프레미스 Active Directory 도메인이 Azure AD와 연결되면 사용자의 장치에서는 연결된 Azure AD 계정을 사용하여 설정을 동기화하려고 시도하기 시작합니다. Azure AD 관리자가 엔터프라이즈 상태 로밍을 활성화하지 않으면 연결된 Azure AD 계정이 있는 사용자가 설정 동기화를 중지하게 됩니다. Azure AD를 통해 설정 동기화가 활성화되면 사용자가 Azure AD ID로 로그인하는 즉시 Windows 10 사용자가 Azure AD를 사용하여 Windows 설정 동기화를 시작합니다.

회사 장치에 개인 데이터를 저장하는 사용자는 Windows OS 및 응용 프로그램 데이터가 Azure AD와 동기화된다는 사실에 주의하셔야 합니다. 이 값은 다음과 같은 의미를 갖습니다.

- Microsoft 계정 및 Azure AD 설정 동기화에서 별도 계정을 사용하고 있기 때문에 개인 Microsoft 계정 사용자는 해당 설정을 회사 또는 학교 Azure AD 계정과 점점 "달라지게" 됩니다.
- 연결된 Microsoft 계정을 통해 이미 동기화된 Wi-Fi 암호, 웹 자격 증명 및 Internet Explorer 즐겨찾기와 탭 등 개인 데이터는 Azure AD를 통해 동기화됩니다.


## Microsoft 계정과 Azure AD 엔터프라이즈 상태 로밍의 상호 운용성이 어떻게 됩니까?
Windows 10의 2015년 11월 이후 릴리스에서 엔터프라이즈 상태 로밍은 한 번에 한 계정에만 지원됩니다. 사용자가 회사 및 학교 Azure AD 계정을 사용하여 Windows에 로그인하면 모든 데이터가 Azure AD를 통해 동기화됩니다. 사용자가 개인용 Microsoft 계정을 사용하여 Windows에 로그인하면 모든 데이터가 Microsoft 계정을 통해 동기화됩니다. 앱 라이선스의 소유자가 기본 계정인 경우에만 유니버설 앱 데이터는 장치의 기본 로그인 계정을 사용하여 로밍됩니다. 소유자가 보조 계정인 앱에 대한 유니버설 앱 데이터는 동기화됩니다.

## 여러 테넌트의 Azure AD 계정에 대한 설정이 동기화됩니까?
여러 Azure AD 테넌트의 여러 Azure 계정이 동일한 장치에 있는 경우 각 Azure AD 테넌트의 Azure RMS(Rights Management Service)와 통신하도록 장치 레지스트리를 업데이트해야 합니다.

1. 먼저, 각 Azure AD 테넌트에 대한 GUID가 필요합니다. Azure 클래식 포털을 열고 Azure AD 테넌트를 선택합니다. 테넌트에 대한 GUID는 브라우저 주소 표시줄의 URL에 있으며 다음과 같습니다. `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. GUID를 가져온 후에는 다음 레지스트리 키를 추가해야 합니다. **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<tenant ID GUID>** <**tenant ID GUID**> 키에서 **AllowedRMSServerUrl**이라는 이름의 새로운 다중 문자열 값(REG-MULTI-SZ)과 해당 데이터를 만들고, 장치가 액세스하는 다른 Azure 테넌트의 라이선싱 배포 지점을 지정합니다.
3. **Get-AadrmConfiguration** cmdlet을 실행하여 라이선싱 배포 지점 URL을 찾을 수 있습니다. **LicensingIntranetDistributionPointUrl **및 **LicenseingExtranetDistributionPointUrl**의 값이 다르면 두 값을 모두 지정합니다. 값이 같으면 값을 한 번만 지정합니다.


## 기존 Windows 데스크톱 응용 프로그램의 로밍 설정에 대한 옵션은 무엇인가요?
로밍은 유니버설 Windows 앱에서만 작동합니다. 기존 Windows 데스크톱 응용 프로그램에서 로밍을 활성화할 수 있는 두 가지 옵션이 있습니다.

- [데스크톱 브리지](http://aka.ms/desktopbridge)를 사용하면 유니버설 Windows 플랫폼에 기존 Windows 데스크톱 앱을 쉽게 가져올 수 있습니다. 이때 Azure AD 앱 데이터 로밍을 활용하려면 최소한의 코드 변경 작업이 필요합니다. 데스크톱 브리지는 앱 ID를 사용하여 앱을 제공하며 이는 기존 데스크톱 앱에 앱 데이터 로밍을 사용하는 데 필요합니다.
- [UE-V(사용자 경험 가상화)](https://technet.microsoft.com/library/dn458947.aspx)를 사용하면 기존 Windows 데스크톱 앱에 대한 사용자 지정 설정 템플릿을 만들 수 있고 Win32 앱에 대해서만 로밍을 사용할 수 있습니다. 이 옵션은 앱의 코드를 변경하기 위해 앱 개발자가 필요하지 않습니다. UE-V는 Microsoft Desktop Optimization 패키지를 구매한 고객에 대한 온-프레미스 Active Directory 로밍으로 제한됩니다. 

관리자는 [UE-V 그룹 정책](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)을 통해 Windows OS 설정 및 유니버설 앱 데이터의 로밍을 사용하지 않도록 설정하여 Windows 데스크톱 앱 데이터를 로밍하도록 UEV를 구성할 수 있습니다.

- "Windows 설정 로밍" 그룹 정책 사용 안 함
- "Windows 앱 동기화 안 함" 그룹 정책 사용하도록 설정

앞으로 Microsoft는 UE-V를 Windows와 긴밀하게 통합하고 Azure AD 클라우드를 통해 설정을 로밍할 수 있도록 UE-V를 확장하는 방법을 연구할 것입니다.


## 동기화된 설정 및 데이터를 온-프레미스에 저장할 수 있나요?
엔터프라이즈 상태 로밍은 Azure 클라우드에서 동기화된 모든 데이터를 저장합니다. 이 때 UE-V에서는 온-프레미스 로밍 솔루션을 필요로 하는 엔터프라이즈에 온-프레미스 로밍 솔루션을 제공합니다.

## 로밍되는 데이터를 소유한 사람은 누구인가요?
엔터프라이즈는 엔터프라이즈 상태 로밍을 통해 로밍되는 데이터를 소유합니다. 데이터는 Azure 데이터 센터에 저장됩니다. 모든 사용자 데이터는 Azure RMS(Azure Rights Management)를 사용하여 암호화된 상태로 전송되고 클라우드에 보관됩니다. 이는 사용자 자격 증명 같은 민감한 특정 데이터만 장치에서 내보내기 전에 암호화하는 Microsoft 계정 기반 설정 동기화에 비해 향상된 기능입니다.

Microsoft는 고객 데이터를 보호하기 위해 최선을 다하고 있습니다. 엔터프라이즈 사용자의 설정 데이터는 Windows 10 장치에서 나오기 전에 Azure RMS를 통해 암호화되므로 다른 사용자가 이 데이터를 읽을 수 없습니다. 조직에서 Azure RMS에 대한 유료 구독을 보유한 경우 문서 추적 및 해제, 민감한 정보가 포함된 전자 메일 자동 보호, 사용자 고유 키("BYOK(Bring Your Own Key"라고도 함) 관리 등 다른 Azure RMS 기능을 사용할 수 있습니다. 이러한 기능 및 Azure RMS의 작동 방식에 대한 자세한 내용은 [Azure Rights Management란](https://technet.microsoft.com/jj585026.aspx)을 참조하세요.

## 특정 앱 또는 설정에 대한 동기화를 관리할 수 있나요?
Windows 10에는 개별 응용 프로그램의 로밍을 비활성화하는 MDM 또는 그룹 정책 설정이 없습니다. 테넌트 관리자는 관리되는 장치의 모든 앱에 대해 앱 데이터 동기화를 비활성화할 수 있지만 앱당 또는 앱 내부 수준에서 더욱 정교하게 제어하는 방법은 없습니다.

## 개별 사용자가 로밍을 활성화/비활성화하려면 어떻게 해야 하나요?
**설정** 앱에서 **계정** ->** 설정 동기화**로 이동합니다. 이 페이지에서 설정 로밍에 사용되는 계정을 확인할 수 있으며, 개별 설정 그룹의 로밍을 활성화 또는 비활성화할 수 있습니다.

##현재 Microsoft는 Windows 10 로밍 활성화에 대해 무엇을 권장합니까?
Microsoft에서는 사용자 프로필 로밍, UE-V, 엔터프라이즈 상태 로밍을 포함하여 몇 가지 설정 로밍 솔루션을 제공하고 있습니다. Microsoft에서는 이후 버전의 Windows에서도 엔터프라이즈 상태 로밍에 투자하기 위해 최선을 다하고 있습니다. 조직에서 데이터를 클라우드로 이동할 준비가 덜 되었거나 불편하게 생각하는 경우에는 기본 로밍 기술로 UE-V를 사용하시기를 권장합니다. 조직에서 기존 Windows 데스크톱 응용 프로그램에 대한 로밍 지원이 필요하지만 클라우드로 이동하고 싶은 경우 Microsoft에서는 엔터프라이즈 상태 로밍과 UE-V를 모두 사용하시기를 권장합니다. UE-V와 엔터프라이즈 상태 로밍이 매우 비슷한 기술이지만 상호 배타적인 관계는 아니며 현재는 조직이 사용자에게 필요한 로밍 서비스를 제공할 수 있도록 서로를 보완해 주는 관계에 있습니다.

엔터프라이즈 상태 로밍과 UE-V를 모두 사용할 경우 다음 규칙이 적용됩니다.

- 엔터프라이즈 상태 로밍은 장치의 기본 로밍 에이전트입니다. UE-V는 “Win32 갭”을 보완하기 위해 사용됩니다. 
- Windows 설정 및 최신 UWP 앱 데이터는 이미 엔터프라이즈 상태 로밍을 통해 다루었으므로 UE-V 그룹 정책을 사용하여 비활성화해야 합니다. 

##엔터프라이즈 상태 로밍은 VDI(가상 데스크톱 인프라)를 어떻게 지원하나요?
엔터프라이즈 상태 로밍은 서버 SKU가 아닌 Windows 10 클라이언트 SKU에 대해서만 지원됩니다. 클라이언트 VM이 가상 컴퓨터에 대한 하이퍼바이저 컴퓨터와 최종 사용자 원격 컴퓨터에서 호스팅되는 경우 사용자의 데이터가 로밍됩니다. 여러 사용자가 전체 데스크톱 환경을 위해 서버에 동일한 OS 및 사용자 원격 컴퓨터를 공유하는 경우 로밍은 작동되거나 작동되지 않을 수 있습니다. 두 번째 세션 기반 시나리오는 공식적으로 지원되지 않습니다.


## 조직에서 한동안 로밍을 사용하다가 Azure RMS를 구입하면 어떻게 되나요?
조직에서 Azure RMS 사용 제한된 무료 구독을 통해 Windows 10에서 이미 로밍을 사용 중인 경우에는 유료 Azure RMS 구독을 구입해도 로밍 기능에 영향을 주지 않으며 IT 관리자가 구성을 변경할 필요가 없습니다.

## 알려진 문제

- 스마트 카드 또는 가상 스마트 카드로 Windows에 로그인하면 설정 동기화가 중지됩니다. 스마트 카드 또는 가상 스마트 카드를 사용하여 장치에 로그인하려고 하면 동기화가 중지됩니다. 이 문제는 추후에 있을 Windows 10 업데이트에서 해결될 것입니다.
- IE 즐겨찾기 동기화는 이전 버전 Windows 10을 작성하기 위해 작동하지 않습니다. 작업에 동기화된 IE 즐겨찾기용 Windows 10에 대한 5월 누적 업데이트가 필요합니다(10.0.10586.318 빌드 이상).
- 특정 조건에서 엔터프라이즈 상태 로밍은 Multi-Factor Authentication(MFA)가 구성된 경우 데이터를 동기화하는 데 실패할 수 있습니다. 
    - Azure AD 포털에서 [Azure MFA](multi-factor-authentication.md)를 필요로 하도록 사용자를 구성한 경우 사용자는 암호를 사용하여 Windows 10 장치에 로그인하는 동안 설정을 동기화하는 데 실패할 수 있습니다. 이러한 형식의 MFA 구성은 Azure 관리자 계정을 보호하도록 계획되었습니다. 관리자 사용자는 Office 365와 같은 다른 Azure 서비스에 액세스하는 동안 [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) PIN을 사용하거나 Multi-Factor Authentication을 완료하는 해당 Windows 10 장치에 로그인하여 동기화될 수 있습니다. 
    - 관리자가 AD FS MFA 조건부 액세스 정책을 구성하고 장치에 대한 액세스 토큰이 만료되는 경우 동기화는 실패할 수 있습니다. Office 365와 같은 다른 Azure 서비스에 액세스하는 동안 [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) PIN을 사용하거나 Multi-Factor Authentication을 완료하여 로그오프하고 로그인하도록 합니다.



## 관련된 항목
- [엔터프라이즈 상태 로밍 개요](active-directory-windows-enterprise-state-roaming-overview.md)
- [Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화](active-directory-windows-enterprise-state-roaming-enable.md)
- [설정 동기화에 대한 그룹 정책 및 MDM 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 로밍 설정 참조](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0608_2016-->