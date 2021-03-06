---
title: "Azure AD Connect: Seamless Single Sign-On | Microsoft 문서"
description: "이 항목에서는 Azure AD(Azure Active Directory)의 Seamless Single Sign On 및 이를 사용하여 회사 네트워크 내부에서 회사 데스크톱 사용자에 대한 실제 Single Sign-On을 제공하는 방법을 설명합니다."
services: active-directory
keywords: "Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2601850f99188445cf63a6a4f185bdc4ebb92c29
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory의 Seamless Single Sign-On

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory의 Seamless Single Sign-On이란?

Azure AD의 Seamless SSO(Azure Active Directory Seamless Single Sign On)는 회사 네트워크에서 연결된 회사 데스크톱에 로그인한 사용자에 대한 실제 Single Sign-On을 제공합니다. 이 기능이 사용하도록 설정되면 Azure AD에 로그인할 때 사용자가 암호를 입력할 필요가 없고 대부분의 경우에는 사용자 이름도 입력할 필요가 없습니다. 이 기능을 사용하면 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 서비스에 쉽게 액세스할 수 있습니다.

Seamless SSO는 Azure AD Connect를 통해 사용하도록 설정되고 [암호 동기화](active-directory-aadconnectsync-implement-password-synchronization.md) 또는 [통과 인증](active-directory-aadconnect-pass-through-authentication.md)과 결합될 수 있습니다.

>[!NOTE]
>이 기능은 이미 이 기능을 제공하는 ADFS(Active Directory Federation Services)에 적용할 수 없습니다.

이 기능을 특정 사용자에게 적용하려면 다음 조건을 충족해야 합니다.

- 사용자가 회사 데스크톱에 로그인하고 있습니다.
- 데스크톱이 이전에 AD(Active Directory) 도메인에 가입되었습니다.
- 데스크톱은 회사의 유선/무선 네트워크 또는 원격 액세스 연결(예: VPN 연결)을 통해 DC(도메인 컨트롤러)에 직접 연결됩니다.
- 브라우저의 인트라넷 영역에 대한 서비스 끝점이 포함되었습니다.

위 조건을 충족하지 않으면 이전과 같이 사용자 이름과 암호를 요청하는 메시지가 사용자에게 표시됩니다.

![Seamless Single Sign-On](./media/active-directory-aadconnect-sso/sso1.png)

Seamless SSO의 다른 기능과 다음과 같습니다.

- Azure AD 로그인 요청에 `domain_hint` 또는 `login_hint` 매개 변수가 포함되어 있으면(테넌트의 응용 프로그램에서 시작) Seamless SSO가 이를 활용하므로 사용자가 자신의 사용자 이름과 암호를 입력하지 않아도 됩니다.
- Seamless SSO는 사용자 이름으로 온-프레미스 기본 사용자 이름(일반적으로 "userPrincipalName") 또는 Azure AD Connect에 구성된 또 다른 특성("대체 ID"라고 함)을 지원합니다.
- Seamless SSO는 편의적인 기능입니다. 즉 SSO가 어떤 이유로 실패하는 경우 사용자 로그인 환경은 일반 동작으로 대체되므로 사용자는 로그인 페이지에 암호를 입력해야 합니다.

## <a name="whats-available-during-preview"></a>미리 보기 중에 무엇을 사용할 수 있나요?

>[!NOTE]
>Azure AD의 Seamless SSO는 현재 미리 보기로 제공됩니다. 무료 기능이며 이 기능을 사용하는 데는 Azure AD 유료 버전이 필요하지 않습니다.

Seamless SSO는 Windows 기반 데스크톱과 같이 Kerberos 인증이 가능한 데스크톱에서 [최신 인증](https://aka.ms/modernauthga)을 지원하는 웹 브라우저 기반 클라이언트 및 Office 클라이언트를 통해 지원됩니다. 아래 행렬은 다양한 운영 체제의 브라우저 기반 클라이언트에 대한 세부 정보를 제공합니다.

| OS\Browser |Internet Explorer|Google Chrome|Mozilla Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|예|예|예\*|아니요
|Windows 8.1|예|예|예\*|해당 없음
|Windows 8|예|예|예\*|해당 없음
|Windows 7|예|예|예\*|해당 없음
|Mac OS X|해당 없음|예\*|예\*|해당 없음

\*추가 구성이 필요합니다.

>[!NOTE]
>Windows 10의 경우 최적의 Azure AD 환경을 위해 [Azure AD 조인](../active-directory-azureadjoin-overview.md)을 사용할 것을 권장합니다.

## <a name="how-does-azure-ad-seamless-sso-work"></a>Azure AD의 Seamless SSO 작동 방식

[아래](#how-to-enable-azure-ad-seamless-sso?)와 같이 Azure AD Connec에서 Seamless SSO를 사용하도록 설정할 수 있습니다. 이 기능이 사용하도록 설정되면 온-프레미스 AD(Active Directory)에 AZUREADSSOACCT라는 컴퓨터 계정이 생성되고 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다. 또한 Azure AD 로그인 중에 사용되는 두 개의 서비스 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 주체 이름)이 생성됩니다.

>[!NOTE]
> Azure AD Connect를 통해 Azure AD로 동기화하는 각 AD 포리스트에 대해, 그리고 Seamless SSO를 사용할 사용자에 대해 컴퓨터 계정 및 Kerberos SPN을 생성해야 합니다. AD 포리스트에 컴퓨터 계정에 대한 조직 구성 단위(OU)가 있는 경우 Seamless SSO 기능을 사용하도록 설정한 후 AZUREADSSOACCT 컴퓨터 계정을 OU로 이동하여 계정이 삭제되지 않고 다른 컴퓨터 계정과 동일한 방식으로 관리되는지 확인합니다.

이 설치가 완료되면 Azure AD 로그인은 IWA(Windows 통합 인증)를 사용하는 다른 로그인과 같은 방식으로 작동합니다. Seamless SSO 프로세스는 다음과 같이 작동합니다.

사용자가 SharePoint Online과 같이 Azure AD로 보호되는 클라우드 기반 리소스에 액세스하려는 경우를 살펴보겠습니다. SharePoint Online은 로그인을 위해 사용자 브라우저를 Azure AD로 리디렉션합니다.

Azure AD에 대한 로그인 요청에 `domain_hint`(Azure AD 테넌트 식별(예: contoso.onmicrosoft.com)) 또는 `login_hint`(사용자의 사용자 이름 식별(예: user@contoso.onmicrosoft.com 또는 user@contoso.com)) 매개 변수가 포함되면 1-5단계가 수행됩니다.

두 매개 변수 중 하나가 요청에 포함되지 않으면 Azure AD 로그인 페이지에 사용자 이름을 입력하라는 메시지가 표시됩니다. 사용자가 사용자 이름 필드를 탭하거나 [계속] 단추를 클릭한 후에만 1-5단계가 수행됩니다.

1. Azure AD는 Kerberos 티켓을 제공하기 위해 401 권한 없음 응답을 통해 클라이언트에 통신을 시도합니다.
2. 클라이언트는 Active Directory로부터 Azure AD에 대한 티켓을 요청합니다(앞에서 설정된 컴퓨터 계정으로 표시됨).
3. Active Directory는 컴퓨터 계정을 찾아서 컴퓨터 계정의 비밀로 암호화된 Kerberos 티켓을 클라이언트에 반환합니다. 티켓에는 데스크톱에 현재 로그인한 사용자의 ID가 포함됩니다.
4. 클라이언트는 Active Directory에서 받은 Kerberos 티켓을 Azure AD에 보냅니다.
5. Azure AD는 이전에 공유된 키를 사용하여 Kerberos 티켓의 암호를 해독합니다. 성공한 경우 Azure AD는 리소스의 요청에 따라 토큰을 반환하거나 사용자에게 다단계 인증과 같은 추가적인 증명을 수행하도록 요청합니다.

Seamless SSO는 편의적인 기능입니다. 즉 SSO가 어떤 이유로 실패하는 경우 사용자 로그인 환경은 일반 동작으로 대체되므로 사용자는 로그인 페이지에 암호를 입력해야 합니다.

이 프로세스는 아래 다이어그램에 나와 있습니다.

![Seamless Single Sign-On](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>Azure AD Seamless SSO를 사용하도록 설정하는 방법

### <a name="pre-requisites"></a>필수 조건

통과 인증으로 Seamless SSO를 사용하는 경우에 다음과 같은 통과 인증 기능에 필요한 것보다 많은 추가 필수 조건이 없습니다.

암호 동기화로 Seamless SSO를 사용하는 경우에 Azure AD Connect와 Azure AD 간의 방화벽이 있는 경우 다음 사항을 확인합니다.

- Azure AD Connect 서버는 `*.msappproxy.net` URL과 통신할 수 있습니다.
- Azure AD Connect(버전 1.1.484.0 이상)가 포트 443을 통해 Azure AD에 대해 HTTPS 요청을 수행할 수 있습니다. 이 기능은 기능을 사용하도록 설정하는 데만 사용되고 실제 사용자 로그인에는 사용되지 않습니다.
- Azure AD Connect가 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 대한 직접적인 IP 연결을 생성할 수도 있습니다. 다시, 기능을 사용하도록 설정하는 데만 사용됩니다.

>[!NOTE]
> Azure AD Connect의 이전 버전(1.1.484.0 이전)은 포트 9090을 통해 Azure AD와 통신할 수 있어야 합니다.

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>Azure AD Seamless SSO 기능 사용

Azure AD Seamless SSO는 Azure AD Connect를 통해 사용하도록 설정할 수 있습니다.

Azure AD Connect를 새로 설치할 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. "사용자 로그인" 페이지에서 "Single Sign-On 사용" 옵션을 선택합니다.

![Azure AD Connect - 사용자 로그인](./media/active-directory-aadconnect-sso/sso8.png)

Azure AD Connect가 [빠른 설치](active-directory-aadconnect-get-started-express.md) 또는 [사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) 경로를 사용하여 설치되어 있으면 Azure AD Connect에서 "사용자 로그인 변경 페이지"를 선택하고 "다음"을 클릭합니다. 그다음에 "Single Sign-On 사용" 옵션을 선택합니다.

![Azure AD Connect - 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

"Single Sign-On 사용" 페이지에 도달할 때까지 마법사를 계속 진행합니다. Azure AD Connect를 통해 Azure AD로 동기화하는 각 AD 포리스트에 대해, 그리고 Seamless SSO를 사용할 사용자에 대해 도메인 관리자 자격 증명을 제공해야 합니다. 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지 않고 앞에서 설명한 대로 컴퓨터 계정을 만들고 Kerberos SPN을 구성하는 데만 사용됩니다.

마법사를 완료하면 SSO Seamless가 테넌트에서 사용하도록 설정됩니다. 사용자가 이 기능을 활용할 수 있으려면 먼저 다음 섹션의 단계를 완료해야 합니다.

## <a name="rolling-the-feature-out-to-your-users"></a>사용자에게 기능 롤아웃

Seamless SSO 기능을 사용자에게 롤아웃하려면 Active Directory의 그룹 정책을 통해 사용자의 인트라넷 영역 설정에 두 Azure AD URL(https://autologon.microsoftazuread-sso.com 및 https://aadg.windows.net.nsatc.net )을 추가해야 합니다. 이는 Internet Explorer 및 Google Chrome(Internet Explorer와 동일한 신뢰할 수 있는 사이트 URL 집합을 공유하는 경우)에서만 작동합니다. Mozilla Firefox에 대해서는 별도로 구성해야 합니다.

### <a name="why-do-you-need-this"></a>필요한 이유

기본적으로 브라우저는 URL이 브라우저 인트라넷 영역의 일부로 정의되어야 Kerberos 티켓을 클라우드 끝점으로 보냅니다. 브라우저는 URL에서 해당 영역(인터넷 또는 인트라넷)을 자동으로 계산합니다. 예를 들어 http://contoso/는 인트라넷 영역에 매핑되지만 http://intranet.contoso.com/은 인터넷 영역에 매핑됩니다(URL에 마침표가 포함되므로).

Seamless SSO에 사용된 Azure AD URL에 마침표가 포함되어 있기 때문에 각 브라우저의 인트라넷 영역 설정에 명시적으로 추가되어야 합니다. 이렇게 하면 브라우저에서 현재 로그인된 사용자의 Kerberos 티켓을 Azure AD에 자동으로 보냅니다. 각 데스크톱에서 이 작업을 수동으로 수행할 수 있지만 모든 사용자에 걸쳐 필요한 URL을 인트라넷 영역에 추가하는 가장 쉬운 방법은 Active Directory에 그룹 정책을 만드는 것입니다.

### <a name="detailed-steps"></a>자세한 단계

1. 그룹 정책 관리 도구를 엽니다.
2. 모든 사용자에게 적용된 그룹 정책(예: **기본 도메인 정책**)을 편집합니다.
3. **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page**로 이동하고 **영역에 대한 사이트 할당 목록**을 선택합니다.
![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png)  
4. 정책을 사용하도록 설정하고 대화 상자에서 다음 값/데이터를 입력합니다. 이는 Kerberos 티켓이 전송되는 Azure AD URL입니다.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
5. **확인**을 클릭하고 **확인**을 다시 클릭합니다.

다음과 같이 표시되어야 합니다.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>기본적으로 Chrome은 Internet Explorer와 동일한 신뢰할 수 있는 사이트 URL 집합을 사용합니다. Chrome에 대해 다른 설정을 구성한 경우에는 해당 설정을 별도로 업데이트해야 합니다.

## <a name="disabling-azure-ad-seamless-sso"></a>Azure AD Seamless SSO를 사용하지 않도록 설정

Azure AD Seamless SSO는 Azure AD Connect를 통해 사용하지 않도록 설정할 수 있습니다.

Azure AD Connect를 실행하고 “사용자 로그인 페이지 변경”(Change user sign-in page)을 선택하고 “다음”을 클릭합니다. 그런 다음 “Single Sign-On 사용” 옵션의 선택을 취소합니다. 마법사를 계속 진행합니다. 마법사를 완료하면 SSO Seamless가 테넌트에서 사용하지 않도록 설정됩니다. 그러나 다음과 같은 메시지가 화면에 표시됩니다.

“Single Sign-On을 이제 사용하지 않도록 설정했습니다. 하지만 정리를 완료하기 위해 수행할 추가 수동 단계가 남아 있습니다. 자세한 정보”

다음은 필요한 수동 단계입니다.

- Seamless SSO가 사용하도록 설정된 AD 포리스트 목록을 가져옵니다.
  - PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 그러면 Azure AD 테넌트 관리자 자격 증명을 입력하라는 팝업 메시지가 표시되어야 합니다.
  - `Get-AzureADSSOStatus`를 호출합니다. 그러면 이 기능이 사용하도록 설정된 AD 포리스트 목록("도메인" 목록처럼 보임)이 표시됩니다.
- 위의 목록에서 찾은 각 AD 포리스트에서 AZUREADSSOACCT 컴퓨터 계정을 수동으로 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [문제 해결 가이드](active-directory-aadconnect-troubleshoot-sso.md)를 읽고 Azure AD Seamless SSO와 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.

## <a name="feedback"></a>사용자 의견

Microsoft는 사용자의 의견을 소중하게 생각합니다. 문의 사항이 있으면 아래의 댓글 섹션을 사용하세요. 새로운 기능을 요청하려면 [UserVoice 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)을 사용하세요.

