---
title: "Azure AD v2 Windows 데스크톱 시작 - 구성 | Microsoft Docs"
description: "Windows Desktop .NET(XAML) 응용 프로그램이 액세스 토큰을 얻고 Azure Active Directory v2 끝점으로 보호되는 API를 호출하는 방식"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c46837af57978b047242b2869243f83d372ee43e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 응용 프로그램의 등록 정보 추가
이 단계에서는 프로젝트에 응용 프로그램 ID를 추가해야 합니다.

1.    `App.xaml.cs`를 열고 다음으로 `ClientId`를 바꿉니다.

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>다음 내용

[테스트 및 유효성 검사](active-directory-mobileanddesktopapp-windowsdesktop-test.md)

