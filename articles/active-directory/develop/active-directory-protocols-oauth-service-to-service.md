---
title: "OAuth 2.0을 사용한 Azure AD 서비스 간 인증 | Microsoft Docs"
description: "이 문서는 OAuth 2.0 클라이언트 자격 증명 부여 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: priyamo
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 9f8513c64cf605ab339dc3dfb8129a7fad123918
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>클라이언트 자격 증명을 사용하여 서비스를 호출하는 서비스(공유 암호 또는 인증서)
OAuth 2.0 클라이언트 자격 증명 부여 흐름은 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스( *기밀 클라이언트*)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Azure AD는 호출 서비스가 자격 증명으로 인증서(공유 암호 대신)를 사용할 수 있도록 합니다.

## <a name="client-credentials-grant-flow-diagram"></a>클라이언트 자격 증명 부여 흐름 다이어그램
다음 다이어그램에서는 Azure Active Directory(Azure AD)에서 클라이언트 자격 증명 부여 흐름이 작동하는 방법에 대해 설명합니다.

![OAuth 2.0 클라이언트 자격 증명 부여 흐름](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. 클라이언트 응용 프로그램은 Azure AD 토큰 발급 끝점 인증하고 액세스 토큰을 요청합니다.
2. Azure AD 토큰 발급 끝점이 액세스 토큰을 발급합니다.
3. 보안 리소스에 인증하는 데 액세스 토큰이 사용됩니다.
4. 보안 리소스의 데이터는 웹 응용 프로그램에 반환됩니다.

## <a name="register-the-services-in-azure-ad"></a>Azure AD에서 서비스 등록
Azure AD(Azure Active Directory)에서 호출 서비스와 수신 서비스를 등록합니다. 자세한 지침은 [Azure Active Directory와 응용 프로그램 통합](active-directory-integrating-applications.md)을 참조하세요.

## <a name="request-an-access-token"></a>액세스 토큰 요청
액세스 토큰을 요청하려면 테넌트별 Azure AD 끝점에 HTTP POST를 사용합니다.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>서비스 간 액세스 토큰 요청
클라이언트 응용 프로그램이 공유 암호 또는 인증서 중에서 어떤 방식으로 보호되도록 선택되는지에 따라 두 가지 사례가 있습니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청
공유 암호를 사용할 경우 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| grant_type |필수 |요청된 부여 유형을 지정합니다. 클라이언트 자격 증명 부여 흐름에서 값은 **client_credentials**이어야 합니다. |
| client_id |필수 |호출 웹 서비스의 Azure AD 클라이언트 ID를 지정합니다. [Azure Portal](https://portal.azure.com)에서 호출 응용 프로그램의 클라이언트 ID를 찾으려면, **Active Directory**를 클릭하고, 디렉터리를 전환한 후, 응용 프로그램을 클릭합니다. client_id는 *응용 프로그램 ID*입니다. |
| client_secret |필수 |Azure AD에서 호출 웹 서비스 또는 디먼 응용 프로그램에 대해 등록된 키를 입력합니다. 키를 생성하려면 Azure Portal에서 **Active Directory**를 클릭하고, 디렉터리를 전환한 후 응용 프로그램을 클릭하고 **설정**을 클릭하고 **키**를 클릭한 후 키를 입력합니다.|
| resource |필수 |수신 웹 서비스의 앱 ID URI를 입력합니다. Azure Portal에서 앱 ID URI을 찾으려면, **Active Directory**을 클릭하고, 디렉터리를 전환하고, 서비스 응용 프로그램을 클릭한 후 **설정** 및 **속성**을 클릭합니다. |

#### <a name="example"></a>예제
다음 HTTP POST는 https://service.contoso.com/ 웹 서비스용 액세스 토큰을 요청합니다. `client_id` 은(는) 액세스 토큰을 요청하는 웹 서비스를 식별합니다.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청
인증서를 사용한 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| grant_type |필수 |요청된 응답 유형을 지정합니다. 클라이언트 자격 증명 부여 흐름에서 값은 **client_credentials**이어야 합니다. |
| client_id |필수 |호출 웹 서비스의 Azure AD 클라이언트 ID를 지정합니다. [Azure Portal](https://portal.azure.com)에서 호출 응용 프로그램의 클라이언트 ID를 찾으려면, **Active Directory**를 클릭하고, 디렉터리를 전환한 후, 응용 프로그램을 클릭합니다. client_id는 *응용 프로그램 ID*입니다. |
| client_assertion_type |필수 |값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`이어야 합니다. |
| client_assertion |필수 | 응용 프로그램에 대해 자격 증명으로 등록한 인증서를 사용해서 만들고 서명해야 하는 어설션(Jason 웹 토큰). 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 다음을 참조하세요.|
| resource | 필수 |수신 웹 서비스의 앱 ID URI를 입력합니다. Azure Portal에서 앱 ID URI을 찾으려면, **Active Directory**을 클릭하고, 디렉터리를 클릭하고, 응용 프로그램을 클릭한 후 **구성**을 클릭합니다. |

client_secret 매개 변수가 두 개의 매개 변수 client_assertion_type 및 client_assertion으로 바뀐다는 것을 제외하고 공유 암호에 따른 요청 사례와 매개 변수는 거의 동일합니다.

#### <a name="format-of-the-assertion"></a>어설션 형식
어설션을 계산하기 위해 선택한 언어로 많은 [Jason 웹 토큰](https://jwt.io/) 라이브러리 중 하나를 사용하려고 할 수 있습니다. 토큰에 의해 전달되는 정보는 다음과 같습니다.

##### <a name="header"></a>헤더

| 매개 변수 |  설명 |
| --- | --- | --- |
| `alg` | **RS256**이어야 함 |
| `typ` | **JWT**여야 함 |
| `x5t` | X.509 인증서 SHA-1 지문이어야 함 |

##### <a name="claims-payload"></a>클레임(페이로드)

| 매개 변수 |  설명 |
| --- | --- | --- |
| `aud` | 대상: **https://login.microsoftonline/*tenant_Id*/oauth2/token**이어야 함 |
| `exp` | 만료 날짜 |
| `iss` | 발급자: client_id(클라이언트 서비스의 응용 프로그램 ID)여야 함 |
| `jti` | GUID: JWT ID |
| `nbf` | 이전이 아님: 토큰을 사용할 수 없게 되는 날짜 |
| `sub` | 주체: `iss`의 경우 client_id(클라이언트 서비스의 응용 프로그램 ID)여야 함 |

##### <a name="signature"></a>서명
서명은 [JSON 웹 토큰 RFC7519 사양](https://tools.ietf.org/html/rfc7519)에 설명된 대로 인증서를 적용하여 계산됩니다.

##### <a name="example-of-a-decoded-jwt-assertion"></a>디코딩된 JWT 어설션 예제
```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
} 
. 
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

##### <a name="example-of-encoded-jwt-assertion"></a>인코딩된 JWT 어설션 예제
다음 문자열은 인코딩된 어설션의 예입니다. 자세히 살펴보면 세 개의 섹션이 점(.)으로 구분된 것을 알 수 있습니다. 첫 번째 섹션은 헤더를 인코딩하고, 두 번째는 페이로드를 인코딩하며, 마지막은 처음 두 섹션 콘텐츠의 인증서로 계산된 서명입니다.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

#### <a name="how-to-register-your-certificate-with-azure-ad"></a>Azure AD에 인증서를 등록하는 방법
인증서 자격 증명을 Azure AD의 클라이언트 응용 프로그램에 연결하려면 응용 프로그램 매니페스트를 편집해야 합니다. 인증서가 있을 때 다음을 계산해야 합니다.
- `$base64Thumbprint` - 인증서 해시의 base64 인코딩
- `$base64Value` - 인증서 원시 데이터의 해시의 base64 인코딩

응용 프로그램 매니페스트에서 키를 식별하는 GUID도 제공해야 합니다(`$keyId`).

클라이언트 응용 프로그램에 대한 Azure Portal 앱 등록에서 **매니페스트** 및 **다운로드**를 클릭합니다.
즐겨 사용하는 텍스트 편집기에서 매티페스트를 열고 다음 스키마를 사용해서 *keyCredentials* 속성을 새 인증서 정보로 바꿉니다.
```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
],
```
응용 프로그램 매니페스트의 편집 내용을 저장하고 **매니페스트**, **업로드**를 차례로 클릭하여 Azure AD로 다시 업로드합니다. keycredentials 속성은 다중 값이므로 풍부한 키 관리를 위해 여러 인증서를 업로드할 수 있습니다.


#### <a name="example-of-request"></a>요청 예제
다음 HTTP POST는 인증서가 있는 https://service.contoso.com/ 웹 서비스용 액세스 토큰을 요청합니다. `client_id` 은(는) 액세스 토큰을 요청하는 웹 서비스를 식별합니다.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>서비스 간 액세스 토큰 응답

성공 응답에는 다음 매개 변수가 있는 JSON OAuth 2.0 응답이 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| access_token |요청된 액세스 토큰입니다. 호출 웹 서비스는 이 토큰을 사용하여 수신 웹 서비스에 인증할 수 있습니다. |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| not_before |액세스 토큰은 사용할 수 있게 되는 시작 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 토큰 유효 기간까지의 기간(초)으로 표시됩니다.|
| resource |수신 웹 서비스의 앱 ID URI입니다. |

#### <a name="example-of-response"></a>응답 예제
다음 예제는 웹 서비스에 액세스 토큰 요청에 대한 성공 응답을 보여줍니다.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>참고 항목
* [Azure AD의 OAuth 2.0](active-directory-protocols-oauth-code.md)
* [공유 암호를 사용한 서비스 간 호출의 C# 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon) 및 [인증서를 사용한 서비스 간 호출의 C# 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

