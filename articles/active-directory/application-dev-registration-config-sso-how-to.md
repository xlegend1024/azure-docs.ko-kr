---
title: "새 다중 테넌트 응용 프로그램을 구성하는 방법 | Microsoft Docs"
description: "Azure AD로 개발 및 등록 중인 사용자 지정 응용 프로그램에 대해 Single Sign-On을 구성하는 방법."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 2a6831cbfb747dd134a9cc17ed0f1925d8cc62d4
ms.lasthandoff: 04/18/2017


---

# <a name="how-to-configure-a-new-multi-tenant-application"></a>새 다중 테넌트 응용 프로그램을 구성하는 방법

앱에서 페더레이션된 SSO(Single Sign-On) 사용은 OpenID Connect, SAML 2.0 또는 WS-Fed에 대해 Azure AD를 통해 페더레이션할 때 자동으로 설정됩니다. Azure AD에 기존 세션이 이미 있음에도 불구하고 최종 사용자가 로그인해야 하는 경우 앱이 잘못 구성되었을 수 있습니다.

* ADAL/MSAL을 사용하는 경우 **PromptBehavior**를 **항상**이 아니라 **자동**으로 설정했는지 확인하세요.

* 모바일 앱을 빌드하는 경우 조정되었거나 조정되지 않은 SSO를 사용하려면 추가 구성이 필요할 수 있습니다.

Android의 경우 [Android에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)을 참조하세요.<br>

iOS의 경우 [iOS에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Android에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[iOS에서 앱 간 SSO를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[AzureAD와 앱 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[AzureAD v2.0 수렴형 앱에 대한 동의 및 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

