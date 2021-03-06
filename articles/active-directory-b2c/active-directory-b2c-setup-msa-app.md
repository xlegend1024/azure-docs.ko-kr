---
title: "Azure Active Directory B2C: Microsoft 계정 구성 | Microsoft Docs"
description: "소비자는 Azure Active Directory B2C에 의해 보호되는 응용 프로그램에서 Microsoft 계정으로 등록하고 로그인할 수 있습니다."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: 866d2b54b0b517cb9984fd5e98840fe3a6c69965
ms.contentlocale: ko-kr
ms.lasthandoff: 05/05/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: 고객에게 Microsoft 계정으로 등록 및 로그인 제공
## <a name="create-a-microsoft-account-application"></a>Microsoft 계정 응용 프로그램 만들기
Azure AD(Active Directory) B2C에서 Microsoft 계정을 ID 공급자로 사용하려면 먼저 Microsoft 계정 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 Microsoft 계정이 필요합니다. 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 가져올 수 있습니다.

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)로 이동하고 Microsoft 계정 자격 증명을 사용하여 로그인합니다.
2. **앱 추가**를 클릭합니다.
   
    ![Microsoft 계정 - 새 앱 추가](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. 응용 프로그램에 대한 **이름**을 제공하고 **응용 프로그램 만들기**를 클릭합니다.
   
    ![Microsoft 계정 - 앱 이름](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. **응용 프로그램 ID**의 값을 복사합니다. 테넌트에서 Microsoft 계정을 ID 공급자로 구성하려면 그 값이 필요합니다.
   
    ![Microsoft 계정 - 응용 프로그램 ID](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. **플랫폼 추가**를 클릭하고 **웹**을 선택합니다.
   
    ![Microsoft 계정 - 플랫폼 추가](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft 계정 - 웹](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. **URI 리디렉션** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다.
   
    ![Microsoft 계정 - URL 리디렉션](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. **응용 프로그램 비밀** 섹션 아래에서 **새 암호 생성**을 클릭합니다. 화면에 표시되는 새 암호를 복사합니다. 테넌트에서 Microsoft 계정을 ID 공급자로 구성하려면 그 값이 필요합니다. 이 암호는 중요한 보안 자격 증명입니다.
   
    ![Microsoft 계정-새 암호 생성](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft 계정-새 암호](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. **고급 옵션** 섹션 아래에서 **Live SDK 지원**라는 상자를 선택합니다. **Save**를 클릭합니다.
   
    ![Microsoft 계정-Live SDK 지원](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Microsoft 계정을 ID 공급자로 구성
1. 다음 단계에 따라 [Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "MSA"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Microsoft 계정**을 선택하고 **확인**을 클릭합니다.
6. **이 ID 공급자 설정** 을 클릭하고 이전에 만든 Microsoft 계정 응용 프로그램의 응용 프로그램 ID 및 암호를 입력합니다.
7. **확인**을 클릭한 다음 **만들기**를 클릭하여 Microsoft 계정 구성을 저장합니다.


