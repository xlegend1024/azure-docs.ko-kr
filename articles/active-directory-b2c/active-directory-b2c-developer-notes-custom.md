---
title: "Azure Active Directory B2C: 사용자 지정 정책을 사용하는 개발자 정보 | Microsoft Docs"
description: "사용자 지정 정책으로 B2C를 구성 및 유지 관리하는 개발자를 위한 정보"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 72447a234c5889f36b10b828d28775fc5995c4d4
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C 사용자 지정 정책 공개 미리 보기에 대한 릴리스 정보
**사용자 지정 정책** 기능 집합은 현재 모든 Azure Active Directory B2C(Azure AD B2C) 고객에 대해 공개 미리 보기에서 평가용으로 제공됩니다.  이 기능 집합은 가장 복잡한 ID 솔루션을 구축하는 고급 ID 개발자를 대상으로 합니다.  

현재, 이 기능 집합을 사용하려면 개발자가 XML 파일 편집을 통해 IEF(Identity Experience Framework)를 직접 구성해야 합니다.  이 구성 방법은 강력하고 복잡합니다.  IEF를 사용하는 고급 ID 개발자는 연습을 완료하고 참조 문서를 확인하여 학습에 시간을 투자할 계획입니다. 

## <a name="features-included-in-this-public-preview"></a>이 공개 미리 보기에 포함된 기능
공개 검토에 포함된 새 기능을 통해 개발자는 다음 작업을 수행할 수 있습니다.
1. 사용자 지정 정책을 사용하여 사용자 지정 인증 사용자 경험 작성 및 업로드
* 클레임 공급자 간의 교환으로 사용자 경험 단계별 설명
* 사용자 경험에서 조건부 분기 정의
2. 사용자 지정 인증 사용자 경험에서 REST API 사용 서비스 통합
3. OpenIDConnect 표준과 호환되는 ID 공급자와 페더레이션 추가
4. SAML 2.0 프로토콜을 준수하는 ID 공급자와 페더레이션 추가



## <a name="terms-of-the-public-preview"></a>공개 미리 보기 약관
1. 새 기능의 사용은 평가 목적으로만 권장됩니다.
2. 새 기능은 프로덕션 환경에서는 사용할 수 없습니다.
3. Service Level Agreement(서비스 수준 약정)는 새 기능4에 적용되지 않습니다. 정식 지원 채널을 통해 지원 요청을 정리할 수 있습니다.
5. 일반 공급에 대해 정해진 날짜는 없습니다.
6. Microsoft의 재량에 따라 어떠한 이유로든, Azure AD B2C 제품 정관의 범위를 벗어나는 시나리오 및 사용자 경험을 고객 ID 및 액세스 관리(CIAM) 플랫폼으로 서비스하도록 플래그를 지정하고 거부 또는 제한할 수 있습니다.

## <a name="responsibilities-for-custom-policy-feature-set-developers"></a>사용자 지정 정책 기능 집합 개발자의 책임
수동 정책 구성은 Azure AD B2C의 기본 플랫폼에 대해 낮은 수준의 액세스 권한을 부여하며 완전히 사용자 지정할 수 있는 보안 프레임워크를 생성합니다.  사용자 지정 ID 공급자, 트러스트 관계, 외부 서비스와의 통합 및 단계별 워크플로의 거의 무한한 순열로 인해, 이를 사용하는 고급 개발자의 요구가 더욱 증가합니다.
공개 미리 보기를 최대한 활용하기 위해 사용자 지정 정책 기능 집합을 사용하는 개발자는 다음을 준수하는 것이 좋습니다.
* IEE(Identity Experience Engine)의 구성 언어 및 키/암호 관리 숙지
* 시나리오 및 사용자 지정 통합의 소유권 가져오기
* 체계적인 시나리오 테스트 수행
* 최소 한 번의 개발/테스트 및 하나의 프로덕션 환경으로 소프트웨어 개발 및 스테이징 모범 사례 준수
* 통합할 ID 공급자 및 서비스(예: 비밀, 서비스에 대한 예약/예약 취소된 변경 내용 추적 등)의 새로운 개발 내용에 대한 정보 얻기. 활성 모니터링을 설정하고 프로덕션 환경의 응답성을 모니터링
* 연락처 메일을 최신 상태로 유지하고 Microsoft 라이브 사이트 팀 이메일에 응답 유지
* Microsoft 라이브 사이트 팀에서 권고할 때 시기 적절하게 조치 


>[!NOTE]
>이러한 기능은 결국 Azure AD 기본 제공 정책에 포함되어 모든 개발자가 보다 쉽게 액세스할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계
[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)

