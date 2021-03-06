---
title: "Microsoft Authenticator 앱 도움말 및 지원 | Microsoft Docs"
description: "Microsoft Authentication 앱 및 Azure Multi-Factor Authentication과 관련된 질문과 대답 목록을 제공합니다."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: kgremban
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 731275c72422a622e1d49f7d362447ef3361338b
ms.lasthandoff: 04/12/2017

---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator 앱 FAQ

이 문서에서는 Microsoft Authenticator 앱과 관련하여 발생하는 일반적인 질문에 대해 대답합니다. 질문에 대한 답변을 찾을 수 없는 경우 [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하세요. 또한 앱의 특정 기능인 [휴대폰 FAQ를 사용하여 로그인](microsoft-authenticator-app-phone-signin-faq.md)에 대한 다른 FAQ도 있습니다.

Microsoft Authenticator 앱은 Azure Authenticator 앱을 대신하며, Azure Multi-factor Authentication을 사용하는 경우 권장됩니다. [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>앱에서 코드란 무엇인가요? 숫자가 계속 카운트다운되는 이유는 무엇인가요?

Microsoft Authenticator 앱을 열면 각각 6자리 또는 8자리 숫자를 추가한 계정이 표시됩니다. 30초 타이머가 카운트다운되는 것을 볼 수 있습니다.

이러한 코드는 계정에 로그인할 때 사용됩니다. 사용자 이름 및 암호를 입력하려면 확인 코드를 입력하라는 메시지가 표시될 수 있습니다. Microsoft Authenticator 앱을 열고 현재 표시 중인 코드를 복사합니다. 로그인 페이지에 해당 코드를 입력하여 완료합니다.

코드가 30초마다 변경되기 때문에 동일한 코드를 두 번 사용하지 않도록 합니다. 기억하기로 되어 있는 암호와 다릅니다. 휴대폰에 액세스할 수 있는 사람만 확인 코드를 안다는 점에서 착안하였습니다.

이 코드에는 인터넷 또는 데이터가 필요하지 않으므로 전화 서비스에 로그인하는 것을 걱정할 필요가 없으며 앱에서 데이터 요금제를 다 사용합니다. 앱을 닫으면 백그라운드에서 계속 실행되지 않으므로 배터리가 소모되지 않습니다. 앱을 닫고 다음에 로그인할 때까지 무시할 수 있습니다.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>앱이 열려 있을 때만 알림을 받게 됩니다. 앱이 열려 있지 않으면 어떤 알림도 수신되지 않습니다.

벨 소리를 켜져 있지만 알림이 수신될 때 소음이나 진동을 발생하지 않으면 앱 설정을 확인하세요. 알림에서 소리 또는 진동을 사용하도록 앱을 설정합니다.

알림이 전혀 수신되지 않으면 다음과 같은 경우인지 확인합니다.

- 휴대폰이 방해 금지 또는 자동 모드인가요? 해당 모드에서는 앱이 알림을 보낼 수 없습니다.
- 다른 앱에서 알림을 받을 수 있나요? 받을 수 없으면 휴대폰의 네트워크 연결이나 Android 또는 Apple의 알림 채널에 문제가 있는 것일 수 있습니다. 휴대폰 설정에서 첫 번째 옵션은 해결할 수 있지만 두 번째 옵션의 경우는 서비스 공급자에게 도움을 요청해야 할 수 있습니다.
- 앱에서 일부 계정에 대해서만 알림을 받을 수 있고 다른 계정의 알림은 받을 수 없나요? 그렇다면 앱에서 문제가 있는 계정을 제거하고 다시 추가하여 푸시 알림을 사용하도록 다시 설정합니다. 

이러한 문제 해결 제안을 시도했으나 여전히 문제가 발생하면 저희에게 진단 로그를 전송한 다음(응용 프로그램 설정으로 이동한 다음 **도움말 및 의견** 및 **로그 보내기** 선택) [Microsoft 인증자 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하고 확인된 문제와 지금까지 시도한 단계를 알려주세요. 

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>인증 코드에 Microsoft Authenticator 응용 프로그램을 이미 사용하고 있습니다. 원 클릭 푸시 알림으로 전환하려면 어떻게 해야 하나요?
푸시 알림을 통한 로그인 승인은 Google 또는 Facebook 같은 타사 계정이 아닌 개인 Microsoft 계정이나 회사 및 학교 Microsoft 계정에만 사용할 수 있습니다. 회사 또는 학교 Microsoft 계정을 갖고 있는 경우 조직에서 이 옵션을 사용하지 않도록 설정할 수 있습니다.

개인 계정으로 Microsoft 계정을 사용하고 있으며 푸시 알림으로 전환하려는 경우 계정을 다시 추가해야 합니다. 장치를 계정에 다시 등록하고 푸시 알림을 설정합니다.  

조직에서는 회사 또는 학교 계정에 Microsoft Authenticator를 사용하는 경우 원 클릭 알림 허용 여부를 결정합니다.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Microsoft 이외의 계정에서도 원 클릭 푸시 알림이 작동하나요?
아니요. 푸시 알림은 Microsoft 계정 및 Azure Active Directory 계정에만 작동합니다. 회사 또는 학교에서 Azure AD 계정을 사용하는 경우 이 기능을 사용하지 않도록 설정할 수 있습니다.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>백업에서 장치를 복원했으며 내 계정 코드가 누락되었거나 작동하지 않습니다. 어떻게 된 건가요?
보안을 위해 앱 백업에서 계정을 복원하지 않습니다.  앱을 복원한 후에 계정을 삭제한 후 다시 추가합니다.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>새 장치가 있습니다. 이전 장치에서 Microsoft Authenticator를 제거하고 새 장치로 이동시키려면 어떻게 해야 하나요?
새 장치에 Microsoft Authenticator 앱을 추가해도 다른 장치에서 자동으로 제거하지 않습니다. 계정에 대해 구성된 장치를 관리하도록 2단계 인증을 관리하는 데 사용한 동일한 웹 사이트를 방문하여 이전 앱을 제거합니다.

개인 Microsoft 계정의 경우 이 웹 사이트는 [계정 보안](https://account.microsoft.com/security) 페이지입니다. 회사 또는 학교 Microsoft 계정의 경우 이 웹 사이트는 [MyApps](https://myapps.microsoft.com) 또는 조직에서 설정한 사용자 지정 포털 중 하나일 수 있습니다.

### <a name="how-do-i-remove-an-account-from-the-app"></a>앱에서 계정을 제거하려면 어떻게 해야 하나요?
* iOS: 메인 화면에서 계정 타일을 지웁니다. **삭제**를 선택합니다.
* Windows Phone: 메인 화면에서 메뉴 단추를 선택한 다음 **계정을 편집**합니다. 계정 이름 옆에 있는 **X**를 누릅니다.
* Android: 메인 화면에서 메뉴 단추를 선택한 다음 **계정을 편집**합니다. 계정 이름 옆에 있는 **X**를 누릅니다.

조직에 등록된 장치가 있는 경우 계정을 제거하도록 추가 단계를 완료해야 합니다. 이러한 장치에서 Microsoft Authenticator 앱은 장치 관리자는 자동으로 등록됩니다. 앱을 완전히 제거하려면 먼저 앱 설정에서 앱 등록을 취소해야 합니다.

### <a name="why-does-the-app-request-so-many-permissions"></a>앱이 너무 많은 권한을 요청하는 이유는 무엇인가요?
요청한 사용 권한의 전체 목록은 다음과 같으며 앱에서 사용하는 방법은 다음과 같습니다. 표시되는 특정 사용 권한은 사용하는 휴대폰의 종류에 따라 달라 집니다.

* **카메라**: 카메라를 사용하여 회사, 학교 또는 Microsoft가 아닌 계정을 추가할 때 QR 코드를 검색합니다.
* **연락처 및 휴대폰**: 개인 Microsoft 계정으로 로그인하는 경우 휴대폰에서 사용하는 기존 계정을 검색하여 프로세스를 단순화하려고 합니다.
* **SMS**: 개인 Microsoft 계정으로 처음 로그인하는 경우 기록된 전화 번호와 일치하는지 확인해야 합니다. 앱을 다운로드한 휴대폰으로 메시지를 보냅니다. 메시지에는 6~8자리 인증 코드가 포함되어 있습니다. 이 코드를 찾아 앱에 입력을 요청하는 대신 문자 메시지에서 찾을 수 있습니다.
* **다른 앱을 통해 가져오기**: ID를 인증하는 알림을 수신할 때 해당 알림을 실행 중일 수 있는 다른 앱을 통해 표시합니다.
* **인터넷의 데이터 수신**: 알림을 보내기 위해 이 사용 권한이 필요합니다.
* **휴대폰 절전 모드 전환 방지**: 조직에서 장치를 등록하는 경우 휴대폰에서 이 정책을 변경할 수 있습니다.
* **진동 제어**: ID를 인증하는 알림을 수신할 때마다 진동할지 여부를 선택하는 옵션이 있습니다.
* **지문 하드웨어 사용**: 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 프로세스를 보다 쉽게 수행하려면 PIN을 입력하는 대신 지문을 사용할 수 있습니다.
* **네트워크 연결 보기**: Microsoft 계정을 추가할 때 앱에는 네트워크/인터넷 연결이 필요합니다.
* **저장소의 내용 읽기**: 앱 설정을 통해 기술 문제를 보고하는 경우에만 이 사용 권한을 사용하게 됩니다. 저장소의 일부 정보를 수집하여 문제를 진단합니다.
* **전체 네트워크 액세스**: ID를 인증하는 알림을 보내는 데 이 사용 권한이 필요합니다.
* **시작 시 실행**: 전화를 다시 시작하는 경우 이 사용 권한을 사용하면 ID를 인증하는 알림을 계속 수신할 수 있습니다.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Microsoft Authenticator 앱에서 사용자가 장치를 잠금 해제하지 않아도 요청을 승인할 수 있도록 허용하는 이유는 무엇인가요?

의도적인 작동입니다. 2단계 인증은 두 가지를 요구합니다. 하나는 사용자가 알고 있는 것이고, 다른 하나는 사용자가 갖고 있는 것입니다. 사용자가 알고 있는 것은 암호입니다. 사용자가 갖고 있는 것은 휴대폰(Microsoft Authenticator 앱을 통해 설정하고 MFA 증명으로 등록된)입니다.  따라서 휴대폰을 갖고 있고 요청을 승인하면 인증의 두 번째 요소에 대한 조건을 충족합니다. 

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>계정 목록에서 잠금 아이콘은 무엇을 의미하나요?

자물쇠 아이콘은 장치가 Azure AD 및 계정에 등록되어 있음을 나타냅니다. iOS에 대한 장치 등록은 Microsoft Intune 등록 중에 발생합니다.

## <a name="next-steps"></a>다음 단계

### <a name="contact-us"></a>문의처
궁금한 내용에 대한 답변이 여기에 없으면 직접 질문을 남겨주세요. [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 질문을 게시하고, 커뮤니티의 도움을 받고, 이 페이지에 의견을 남기세요.


### <a name="related-topics"></a>관련된 항목
* Microsoft 계정에 대한 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* 회사 또는 학교 계정에 [2단계 인증 문제](multi-factor-authentication-end-user-troubleshoot.md)가 있나요?
* [Microsoft Authenticator를 사용하여 휴대폰에서 로그인합니다.](microsoft-authenticator-app-phone-signin-faq.md)


