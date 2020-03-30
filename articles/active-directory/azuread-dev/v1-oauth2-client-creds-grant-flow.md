---
title: Azure AD-tjänst till service-Auth med OAuth2.0 | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder HTTP-meddelanden för att implementera tjänstautentisering till tjänstautentisering med hjälp av beviljade bidragsflöde för OAuth2.0-klientuppgifter.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f2d1eaec80c8925eb7b38af848e29e944f1ebf69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154550"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Service till servicesamtal med klientautentiseringsuppgifter (delad hemlighet eller certifikat)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2.0-klientautentiseringsuppgifterna Grant Flow tillåter en webbtjänst *(konfidentiell klient)* att använda sina egna autentiseringsuppgifter i stället för att personifiera en användare för att autentisera när du anropar en annan webbtjänst. I det här fallet är klienten vanligtvis en mellannivåwebbtjänst, en daemontjänst eller webbplats. För en högre säkerhetsnivå tillåter Azure AD också den anropande tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringsinformation.

## <a name="client-credentials-grant-flow-diagram"></a>Flödesdiagram för klientautentiseringsuppgifter beviljar
I följande diagram beskrivs hur klientautentiseringsuppgifternas bidragsflöde fungerar i Azure Active Directory (Azure AD).

![Bevilja flöde av OAuth2.0-klientuppgifter](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientprogrammet autentiserar till slutpunkten för Azure AD-tokenutfärdande och begär en åtkomsttoken.
2. Slutpunkten för Azure AD-tokenutfärdning utfärdar åtkomsttoken.
3. Åtkomsttoken används för att autentisera till den skyddade resursen.
4. Data från den skyddade resursen returneras till klientprogrammet.

## <a name="register-the-services-in-azure-ad"></a>Registrera tjänsterna i Azure AD
Registrera både den anropande tjänsten och den mottagande tjänsten i Azure Active Directory (Azure AD). Detaljerade instruktioner finns i [Integrera program med Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Begär en åtkomsttoken
Om du vill begära en åtkomsttoken använder du ett HTTP-POST till den klientspecifika Azure AD-slutpunkten.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Begäran om tjänst-till-tjänst-åtkomsttoken
Det finns två ärenden beroende på om klientprogrammet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: Åtkomsttokenbegäran med en delad hemlighet
När du använder en delad hemlighet innehåller en tjänst-till-tjänst-åtkomsttokenbegäran följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |krävs |Anger den begärda bidragstypen. I ett klientautentiseringsbidragsflöde måste värdet **vara client_credentials**. |
| client_id |krävs |Anger Azure AD-klient-ID för den anropande webbtjänsten. Om du vill hitta det anropande programmets klient-ID klickar du på Azure Active Directory i [Azure-portalen](https://portal.azure.com)och klickar på **Appregistreringar**och klickar på programmet. **Azure Active Directory** Det client_id är *applikations-ID* |
| client_secret |krävs |Ange en nyckel som registrerats för det anropande webbtjänst- eller demonprogrammet i Azure AD. Om du vill skapa en nyckel klickar du på Azure Active Directory i **Azure-portalen,** klickar på **Appregistreringar,** klickar på programmet, klickar på **Inställningar,** klickar på **Nycklar**och lägger till en nyckel.  URL-koda denna hemlighet när du tillhandahåller den. |
| resource |krävs |Ange app-ID-URI för den mottagande webbtjänsten. Om du vill hitta app-ID-URI:n klickar du på Azure Active Directory i **Azure-portalen,** klickar på **Appregistreringar,** klickar på tjänstprogrammet och sedan på **Inställningar** och **Egenskaper**. |

#### <a name="example"></a>Exempel
Följande HTTP POST begär en `https://service.contoso.com/` [åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för webbtjänsten. Identifierar `client_id` webbtjänsten som begär åtkomsttoken.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: Åtkomsttokenbegäran med ett certifikat
En tjänst-till-tjänst-åtkomsttokenbegäran med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |krävs |Anger den begärda svarstypen. I ett klientautentiseringsbidragsflöde måste värdet **vara client_credentials**. |
| client_id |krävs |Anger Azure AD-klient-ID för den anropande webbtjänsten. Om du vill hitta det anropande programmets klient-ID klickar du på Azure Active Directory i [Azure-portalen](https://portal.azure.com)och klickar på **Appregistreringar**och klickar på programmet. **Azure Active Directory** Det client_id är *applikations-ID* |
| client_assertion_type |krävs |Värdet måste vara`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |krävs | Ett påstående (en JSON-webbtoken) som du behöver skapa och signera med certifikatet som du registrerade som autentiseringsuppgifter för ditt program. Läs om [certifikatuppgifter](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) om du vill lära dig hur du registrerar certifikatet och formatet på påståendet.|
| resource | krävs |Ange app-ID-URI för den mottagande webbtjänsten. Om du vill hitta app-ID-URI:n klickar du på Azure Active Directory i **Azure-portalen,** klickar på **Appregistreringar,** klickar på tjänstprogrammet och sedan på **Inställningar** och **Egenskaper**. |

Observera att parametrarna är nästan desamma som i fallet med begäran av delad hemlighet förutom att parametern client_secret ersätts med två parametrar: en client_assertion_type och client_assertion.

#### <a name="example"></a>Exempel
Följande HTTP POST begär en `https://service.contoso.com/` åtkomsttoken för webbtjänsten med ett certifikat. Identifierar `client_id` webbtjänsten som begär åtkomsttoken.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Svar på tjänst-till-tjänst-åtkomsttoken

Ett lyckat svar innehåller ett JSON OAuth 2.0-svar med följande parametrar:

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomsttoken. Anropande webbtjänst kan använda den här token för att autentisera till den mottagande webbtjänsten. |
| token_type |Anger tokentypsvärdet. Den enda typ som Azure AD stöder är **Bearer**. Mer information om innehavartoken finns i [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| expires_on |Den tidpunkt då åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC fram till förfallotiden. Det här värdet används för att bestämma livslängden för cachelagrade token. |
| not_before |Den tid från vilken åtkomsttoken blir användbar. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC fram till giltighetstidens utgångstid för token.|
| resource |App-ID-URI för den mottagande webbtjänsten. |

#### <a name="example-of-response"></a>Exempel på svar
I följande exempel visas ett lyckat svar på en begäran om en åtkomsttoken till en webbtjänst.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Se även
* [OAuth 2.0 i Azure AD](v1-protocols-oauth-code.md)
* [Exempel i C# av tjänsten för att serva samtal med en delad hemlighet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) och Prov i [C# av tjänsten till servicesamtal med ett certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
