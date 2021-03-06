<properties
	pageTitle="Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업 | Microsoft Azure"
	description="Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업에 대해 설명"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="kgremban"/>

# Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업
> [AZURE.NOTE] 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

기본 도메인을 사용하면 응용 프로그램 액세스를 위한 내외부 URL로 동일한 URL을 설정할 수 있으므로 사용자는 액세스 위치에 상관 없이 하나의 URL만 기억하면 해당 앱에 액세스할 수 있습니다. 또한 응용 프로그램에 대한 액세스 패널에서 단일 바로 가기를 만들 수 있습니다. Azure AD 응용 프로그램 프록시에 의해 제공되는 기본 도메인을 사용하면 추가 구성 없이도 사용자의 도메인을 사용할 수 있습니다. 사용자 지정 도메인을 사용하는 경우 응용 프로그램 프록시가 도메인을 인식하고 인증서의 유효성을 검사하도록 보장하기 위해 몇 가지를 수행해야 합니다.

## 사용자 지정 도메인 선택(영문)

1. [응용 프로그램 프록시로 응용 프로그램 게시](active-directory-application-proxy-publish.md)에 있는 지침에 따라 응용 프로그램을 게시합니다.
2. 응용 프로그램이 응용 프로그램 목록에 나타나면 선택하고 **구성**을 클릭합니다.
3. **외부 URL** 아래에 사용자 지정 도메인을 입력합니다.
4. 외부 URL이 https인 경우 Azure가 응용 프로그램의 URL의 유효성을 검사할 수 있도록 인증서를 업로드하라는 메시지가 표시됩니다. 응용 프로그램의 외부 URL과 일치하는 와일드 카드 인증서를 업로드할 수도 있습니다. 이 도메인은 [Azure 확인된 도메인](https://msdn.microsoft.com/library/azure/jj151788.aspx) 목록 안에 있어야 합니다. Azure는 응용 프로그램에 대한 외부 URL과 일치하는 응용 프로그램 또는 와일드카드 인증서의 도메인 URL에 대한 인증서가 있어야 합니다.
5. 사용자 응용 프로그램 목록 내 응용 프로그램에 대한 내외부 액세스와 단일 바로 가기를 위해 동일한 URL을 갖게 해주는 응용 프로그램에 내부 URL을 라우팅하는 DNS 기록을 추가합니다.

## 사용자 지정 도메인 작업에 대한 질문과 대답

Q: 이미 업로드한 인증서를 다시 업로드하지 않고 선택할 수 있습니까? A: 이전에 업로드된 인증서는 자동으로 응용 프로그램에 바인딩되며 응용 프로그램의 호스트 이름과 정확히 일치하는 하나의 인증서가 있습니다.

Q: 인증서를 어떻게 추가하며 내보낸 인증서를 어떤 형식으로 업로드해야 하나요? A: 응용 프로그램 구성 페이지에서 인증서를 업로드해야 합니다. 인증서는 PFX 파일이어야 합니다.

Q: ECC 인증서를 사용할 수 있나요? A: 서명 메서드에 명시적 제한은 없습니다.

Q: SAN 인증서를 사용할 수 있나요? A: 예.

Q: 와일드카드 인증서를 사용할 수 있나요? A: 예.

Q: 각 응용 프로그램에서 다른 인증서를 사용할 수 있나요? A: 예, 두 응용 프로그램이 같은 외부 호스트를 공유하지 않는다면 가능합니다.

Q: 새 도메인을 등록한다면 이 도메인을 사용할 수 있습니까? A: 예, 도메인 목록은 테넌트의 확인된 도메인 목록에서 공급됩니다.

Q: 인증서가 만료되면 어떻게 되나요? A: 응용 프로그램 구성 페이지의 인증서 섹션에 경고가 표시됩니다. 사용자가 응용 프로그램에 액세스하려는 경우 보안 경고가 팝업됩니다.

Q: 지정된 앱에 대한 인증서를 바꾸려면 어떻게 해야 하나요? A: 응용 프로그램 구성 페이지에서 새 인증서를 업로드합니다.

Q: 인증서를 삭제하고 바꿀 수 있나요? A: 새 인증서를 업로드할 때 기존 인증서가 다른 응용 프로그램에서 사용 중이지 않으면 자동으로 삭제됩니다.

Q: 인증서가 해지되면 어떻게 되나요? A: 인증서에 대해 해지 검사는 수행되지 않습니다. 사용자가 응용 프로그램에 액세스하려고 할 때 브라우저에 따라서 보안 경고가 표시될 수 있습니다.

Q: 자체 서명된 인증서를 사용할 수 있나요? A: 예, 자체 서명된 인증서가 허용됩니다. 개인 인증 기관을 사용하는 경우 인증서에 대한 CDP(인증서 해지 지점 배포 지점)는 공용이어야 합니다.

Q: 내 테넌트에 대한 모든 인증서를 볼 수 있는 곳이 있습니까? A: 현재 버전에서는 지원되지 않습니다.


## 참고 항목

- [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)
- [Azure AD에 사용자 지정 도메인 이름 추가](active-directory-add-domain.md)

최신 뉴스 및 업데이트는 [응용 프로그램 프록시 블로그](http://blogs.technet.com/b/applicationproxyblog/)를 확인하세요.

<!---HONumber=AcomDC_0511_2016-->