---
title: Azure Active Directory B2C | Microsoft Docs
description: "암시적 흐름을 사용하여 단일 페이지 앱을 직접 빌드하는 방법"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: f2eb1f67c5687ac9bd8b94f6480617e6dc87ac9f
ms.openlocfilehash: ace35adc1cd2745ea11cb60429a1e7892d1f66d3


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-oauth-20-implicit-flow"></a>Azure Active Directory B2C: OAuth 2.0 암시적 흐름을 사용하는 단일 페이지 앱 로그인

> [!NOTE]
> 이 기능은 아직 미리 보기 버전입니다.
> 

대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 단일 페이지 앱 프런트 엔드가 있습니다. 주로 브라우저에서 실행되는 단일 페이지 앱 및 기타 JavaScript 앱에는 인증과 관련된 흥미로운 난제가 몇 가지 있습니다.

* 이러한 앱의 보안 특성은 기존의 서버 기반 웹 응용 프로그램과 상당히 다릅니다.
* 다수의 인증 서버 및 ID 공급자는 CORS 요청을 지원하지 않습니다.
* 앱에서 멀어지는 전체 페이지 브라우저 리디렉션은 사용자 환경에 특히 방해가 됩니다.

이러한 응용 프로그램을 지원하기 위해 Azure AD B2C는 OAuth 2.0 암시적 흐름을 사용합니다.  OAuth 2.0 암시적 권한 부여 흐름은 [OAuth 2.0 사양의 4.2 섹션](http://tools.ietf.org/html/rfc6749)(영문)에서 설명하고 있습니다.  이 흐름에서 앱은 서버 간 교환 없이 Azure AD 인증 끝점에서 직접 토큰을 받습니다. 모든 인증 논리 및 세션 처리가 추가 페이지 리디렉션 없이 전적으로 JavaScript 클라이언트에서 발생합니다.

Azure AD B2C는 표준 OAuth 2.0 암시적 흐름을 확장하여 단순한 인증 및 권한 부여보다 더 많은 작업을 수행합니다. [**정책 매개 변수**](active-directory-b2c-reference-policies.md)를 소개하며 OAuth 2.0을 사용하여 등록, 로그인 및 프로필 관리와 같은 앱에 사용자 환경을 추가할 수 있습니다. 여기서는 암시적 흐름과 Azure AD를 사용하여 단일 페이지 응용 프로그램에서 이러한 환경 각각을 구현하는 방법을 보여 줍니다.  또한 시작하는 데 도움이 되는 [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) 또는 [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) 샘플을 살펴볼 수 있습니다.

아래 예제 HTTP 요청은 샘플 B2C 디렉터리인 **fabrikamb2c.onmicrosoft.com**과 샘플 응용 프로그램 및 정책을 사용합니다. 이러한 값을 사용하여 직접 요청을 시도하거나 고유의 작업으로 바꿀 수 있습니다.
[사용자 고유의 B2C 디렉터리, 응용 프로그램 및 정책을 가져오는](#use-your-own-b2c-tenant)방법을 알아봅니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램
전체 암시적 로그인 흐름은 다음과 같으며, 각 단계에 대해 아래에서 자세히 설명합니다.

![OpenId Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>인증 요청 보내기
웹앱에서 사용자를 인증하고 정책을 실행해야 하는 경우 사용자를 `/authorize` 끝점으로 지정합니다. 이는 정책에 따라 사용자가 실제로 조치를 취하고 Azure AD 끝점에서 `id_token`을 가져오는 흐름의 대화형 부분입니다.

이 요청에서 클라이언트는 `p` 매개 변수를 실행하기 위해 `scope` 매개 변수 및 정책에서 사용자로부터 가져와야 할 사용 권한을 나타냅니다. 각각 다른 정책을 사용하여 세 가지 예제가 아래에 제공됩니다.(가독성을 위해 구분선이 있음) 각 요청의 작동 방식에 대해 이해하려면 요청을 브라우저에 붙여 넣고 실행합니다.

#### <a name="use-a-sign-in-policy"></a>로그인 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>등록 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>편집 프로필 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| client_id |필수 |[Azure 포털](https://portal.azure.com/) 이 앱에 할당된 응용 프로그램 ID입니다. |
| response_type |필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다.  response_type `token`을 포함할 수도 있습니다. 여기서 `token`을 사용하면 앱에서 권한 부여 끝점에 두 번째 요청을 수행하지 않고도 권한 부여 끝점에서 즉시 액세스 토큰을 받을 수 있습니다.  `token` response_type을 사용하는 경우 `scope` 매개 변수는 토큰을 발행할 리소스를 나타내는 범위를 포함해야 합니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코딩되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. |
| response_mode |권장 |결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다.  암시적 흐름의 경우 `fragment` 이어야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자를 로그인시키고 **id_tokens** 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다(자세한 내용은 이 문서의 뒷부분 참조). `offline_access` 범위는 웹앱에 대한 선택 사항입니다. 앱이 리소스에 장기간 액세스할 수 있도록 **refresh_token**이 필요함을 나타냅니다. |
| state |권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지)를 인코드하는 데에도 사용됩니다. |
| nonce |필수 |결과 id_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| p |필수 |실행할 정책입니다. B2C 테넌트에 생성된 정책의 이름입니다. 정책 이름 값은 "b2c\_1\_"로 시작해야 합니다. 정책에 대한 자세한 내용은 [확장 가능한 정책 프레임워크](active-directory-b2c-reference-policies.md)를 참조하세요. |
| prompt |옵션 |필요한 사용자 상호 작용 유형입니다. 현재 유효한 값은 'login'뿐이며 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 때 사용자에게 정책의 워크플로 완료하도록 요청합니다. 해당 사용자 이름과 암호 입력, 소셜 ID로 로그인, 디렉터리에 등록하는 사용자 또는 정책을 정의하는 방식에 따라 다른 많은 단계를 포함합니다.

사용자가 정책을 완료하면 Azure AD가 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다. 응답은 위의 경우 각각에, 독립적으로 실행된 정책에 대해 정확히 동일합니다 합니다.

#### <a name="successful-response"></a>성공적인 응답
읽기 쉽도록 줄 바꿈을 포함하여 `response_mode=fragment` 및 `response_type=id_token+token`을 사용하는 성공적인 응답은 다음과 같이 표시됩니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 매개 변수 | 설명 |
| --- | --- |
| access_token |앱에서 요청한 액세스 토큰입니다.  액세스 토큰을 디코딩하지 않거나 그렇지 않은 경우 검사하지 않아야 불투명 문자열로 처리할 수 있습니다. |
| token_type |토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| expires_in |access_token이 유효한 기간(초)입니다. |
| scope |토큰이 유효한 범위이며, 나중에 사용할 토큰 캐싱에 사용할 수 있습니다. |
| id_token |앱이 요청한 id_token입니다. id_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id_token 및 해당 콘텐츠에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에 포함되어 있습니다. |
| state |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수를 포함해야 합니다. | 설명 |
| --- | --- |
| error |발생한 오류의 유형을 분류하는 데 사용되는 오류 코드 문자열입니다. 오류 코드가 오류 처리에 사용될 수 있습니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state |전체 설명은 위 표를 참조하세요. 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다.|

## <a name="validate-the-idtoken"></a>id_token 유효성 검사
id_token을 받는 것만으로는 사용자를 인증하는 데 충분하지 않습니다. id_token의 서명 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

기본 설정의 언어에 따라 JWT의 유효성 검사에 사용할 수 있는 다양한 공개 소스 라이브러리가 있습니다. 사용자 고유의 유효성 검사 논리를 구현하는 대신 이러한 옵션을 탐색하는 것이 좋습니다. 여기의 정보는 올바르게 해당 라이브러리를 사용하는 방법을 파악하는 데 도움이 됩니다.

Azure AD B2C에는 앱이 런타임에 Azure AD B2C에 대한 정보를 가져올 수 있게 해주는 OpenID Connect 메타데이터 끝점이 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 테넌트에서 각 정책에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 정책에 대한 메타데이터 문서는 다음에서 찾을 수 있습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

이 구성 문서의 속성 중 하나는 `jwks_uri`이며, 동일한 정책에 대한 해당 값은 다음과 같습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

id_token을 서명하는 데 어떤 정책을 사용할지(그리고 메타데이터를 인출하는 위치)를 결정하기 위해 두 가지 옵션이 있습니다. 먼저 정책 이름은 id_token의 `acr` 클레임에 포함됩니다. id_token에서 클레임을 구문 분석하는 방법에 대한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 정책을 인코딩한 다음 이를 디코딩하여 어떤 정책을 사용할지 결정하는 것입니다. 두 방법 모두 완벽하게 유효합니다.

OpenID Connect 메타데이터 끝점에서 메타데이터 문서를 획득하면 이 끝점에 위치한 RSA256 공용 키를 사용하여 id_token의 서명의 유효성을 검사할 수 있습니다. 이 끝점에는 항상 여러 키가 나열될 수 있으며, 각각 `kid`로 식별됩니다. id_token의 헤더에는 id_token 서명에 사용된 키를 나타내는 `kid` 클레임도 포함됩니다. [토큰 유효성 검사](active-directory-b2c-reference-tokens.md#token-validation)를 포함하여 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.
<!--TODO: Improve the information on this-->

id_token의 서명에 대한 유효성을 검사한 후에 여러 클레임에도 유효성 검사가 필요합니다. 예를 들어 다음과 같습니다.

* `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
* `aud` 클레임의 유효성을 검사하여 id_token이 앱에 발급된 것인지 확인합니다. 해당 값이 앱의 응용 프로그램  ID여야 합니다.
* `iat` 및 `exp` 클레임의 유효성을 검사하여 id_token이 만료되지 않았는지 확인합니다.

이 밖에도 수행해야 할 유효성 검사가 몇 가지 더 있습니다. 이에 관한 자세한 내용은 [OpenID Connect 핵심 사양](http://openid.net/specs/openid-connect-core-1_0.html)에 나옵니다.  시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자/조직이 앱에 등록했는지 확인
* 사용자에게 적절한 권한 부여/권한이 있는지 확인
* Azure Multi-Factor Authentication과 같은 특정 강도의 인증이 발생했는지 확인

id_token의 클레임에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.

id_token의 유효성을 완전히 검사한 후 사용자와 세션을 시작하고 id_token의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

## <a name="get-access-tokens"></a>액세스 토큰 가져오기
웹앱에서 수행해야 하는 모든 작업이 정책을 실행하는 것이면 다음 몇 개 섹션을 건너뛸 수 있습니다. 이러한 섹션은 Web API에 대해 인증된 호출을 실행해야 하며 Azure AD B2C로 보호되는 웹앱에만 적용됩니다.

사용자가 단일 페이지 앱에 로그인되도록 했으므로 Azure AD에서 보안을 유지하는 웹 API를 호출하기 위한 액세스 토큰을 가져올 수 있습니다.  `token` response_type을 사용하여 토큰을 이미 받았더라도 이 방법을 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반적인 웹앱 흐름에서는 `/token` 끝점에 요청하여 이 작업을 수행합니다.  하지만 끝점은 CORS 요청을 지원하지 않으므로 AJAX 호출에서 토큰을 가져오고 새로 고치도록 하는 것은 불가능합니다.  그 대신, 숨겨진 iFrame에 암시적 흐름을 사용하여 다른 웹 API에 대한 새 토큰을 가져올 수 있습니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| client_id |필수 |[Azure 포털](https://portal.azure.com/) 이 앱에 할당된 응용 프로그램 ID입니다. |
| response_type |필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다.  response_type `token`을 포함할 수도 있습니다. `token` 을 여기서 사용하면 앱은 권한 부여 끝점에 두 번째 요청을 수행하지 않고 권한 부여 끝점에서 즉시 액세스 토큰을 받을 수 있습니다.  `token` response_type을 사용하는 경우 `scope` 매개 변수는 토큰을 발행할 리소스를 나타내는 범위를 포함해야 합니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다.  URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다.  토큰을 가져오려면 관심 있는 리소스에 필요한 모든 범위를 포함합니다. |
| response_mode |권장 |결과 토큰을 앱으로 다시 보내는 데 사용되는 메서드를 지정합니다.  `query`, `form_post` 또는 `fragment` 중 하나일 수 있습니다. |
| state |권장 |토큰 응답에 반환되는 요청에 포함된 값입니다.  원하는 모든 콘텐츠의 문자열일 수 있습니다.  일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다.  상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| nonce |필수 |앱에서 생성한 요청에 포함된 값이며, 결과 id_token에 클레임으로 포함됩니다.  그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다.  값은 일반적으로 요청의 출처를 식별하는 임의의 고유 문자열입니다. |
| prompt |필수 |숨겨진 iFrame에서 토큰을 새로 고치고 가져오려면 `prompt=none`을 사용하여 iFrame이 로그인 페이지에 머물지 않고 즉시 반환되는지 확인합니다. |
| login_hint |필수 |숨겨진 iFrame에서 토큰을 새로 고치고 가져오는 경우 사용자가 지정된 시간에 가질 수 있는 여러 세션을 구분하기 위해 이 힌트에 해당 사용자의 사용자 이름을 포함해야 합니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름을 추출할 수 있습니다. |
| domain_hint |필수 |`consumers` 또는 `organizations` 중 하나일 수 있습니다.  숨겨진 iFrame에서 토큰을 새로 고치고 가져오는 경우 요청에 domain_hint를 포함해야 합니다.  이전 로그인의 id_token에서 `tid` 클레임을 추출하여 사용할 값을 결정합니다.  `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다.  그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

`prompt=none` 매개 변수를 설정하면 이 요청이 즉시 성공하거나 실패하고 응용 프로그램으로 돌아갑니다.  성공적인 응답은 `response_mode` 매개 변수에 지정된 메서드를 사용하여 지정된 `redirect_uri`의 앱으로 전송됩니다.

#### <a name="successful-response"></a>성공적인 응답
`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 매개 변수를 포함해야 합니다. | 설명 |
| --- | --- |
| access_token |앱이 요청한 토큰입니다. |
| token_type |항상 `Bearer`입니다. |
| state |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| scope |access_token이 유효한 범위입니다. |

#### <a name="error-response"></a>오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.  `prompt=none`의 경우, 예상되는 오류는 다음과 같습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다.  어떠한 방식이든 응용 프로그램에 적합한 방식으로 이러한 경우를 처리하도록 선택할 수 있습니다.

## <a name="refreshing-tokens"></a>토큰 새로 고침
`id_token`과 `access_token`은 모두 짧은 시간 안에 만료되므로 앱에서 주기적으로 이러한 토큰을 새로 고칠 준비가 되어 있어야 합니다.  이러한 유형의 토큰을 새로 고치려면 `prompt=none` 매개 변수를 사용하여 위와 동일한 숨겨진 iFrame 요청을 수행하여 Azure AD의 동작을 제어합니다.  새 `id_token`을 받으려면 `response_type=id_token`, `scope=openid` 및 `nonce` 매개 변수를 사용해야 합니다.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기
사용자를 앱에서 로그아웃하도록 하려면 사용자를 Azure AD로 리디렉션하여 로그아웃시킵니다. 그러지 않으면 사용자가 자격 증명을 다시 입력하지 않고 앱을 다시 인증할 수 있습니다. 이는 Azure AD를 사용하는 유효한 Single Sign-On 세션이 있기 때문입니다.

위의 "id_token 유효성 검사" 세션에 설명된 동일한 OpenID Connect 메타데이터 문서에 나열된 `end_session_endpoint`로 사용자를 리디렉션할 수 있습니다.

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| p |필수 |사용자가 응용 프로그램에서 로그아웃하도록 하는 데 사용하는 정책입니다. |
| post_logout_redirect_uri |권장 |성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 포함되지 않은 경우 Azure AD B2C에서 사용자에게 일반 메시지를 표시합니다. |

> [!NOTE]
> 사용자를 `end_session_endpoint`로 지정하면 Azure AD B2C를 사용하여 일부 사용자의 Single Sign-On 상태를 취소하지만 사용자의 소셜 IDP(ID 공급자) 세션에서 해당 사용자를 로그아웃시키지 않습니다. 사용자가 다음 로그인하는 동안 같은 IDP를 선택하는 경우 자격 증명을 입력하지 않아도 다시 인증됩니다. 사용자가 B2C 응용 프로그램에서 로그아웃하려는 경우 반드시 완전히 Facebook 계정을 로그아웃한다는 의미는 아닙니다. 그러나 로컬 계정의 경우 사용자의 세션이 올바르게 종료됩니다.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>사용자 고유의 B2C 테넌트 사용
자신에 대한 이러한 요청을 사용해 보려는 경우 먼저 이러한 세 단계를 수행한 다음 위의 예제 값을 고유한 값으로 바꿉니다.

* [B2C 테넌트를 만들고](active-directory-b2c-get-started.md), 요청에서 테넌트의 이름을 사용합니다.
* [응용 프로그램을 만들어](active-directory-b2c-app-registration.md) 응용 프로그램 ID 및 redirect_uri를 얻을 수 있습니다. 앱에서 **웹앱/Web API**를 포함하고 필요에 따라 **응용 프로그램 암호**를 만들려 합니다.
* [정책을 만들어](active-directory-b2c-reference-policies.md) 정책 이름을 얻습니다.

## <a name="samples"></a>샘플

* [Node.JS를 사용하여 단일 페이지 앱 만들기](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)(영문)
* [.NET을 사용하여 단일 페이지 앱 만들기](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)(영문)




<!--HONumber=Feb17_HO2-->


