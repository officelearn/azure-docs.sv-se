---
title: Azure AD tjänst till tjänst-autentisering med hjälp av OAuth2.0 | Microsoft Docs
description: Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänst till tjänst-autentisering med hjälp av OAuth2.0 bevilja flödet.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8f7c6c2bef747d00188cac2c3601fdad739b92a8
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581853"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Tjänst till tjänst-anrop med klientautentiseringsuppgifter (delad hemlighet eller certifikat)
Den OAuth 2.0-klientautentiseringsuppgifter tillåter en webbtjänst (*konfidentiell klient*) att använda sina egna autentiseringsuppgifter i stället för att personifiera en användare för att autentisera vid anrop av en annan webbtjänst. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemon-tjänst eller webbplats. Azure AD kan också anropa tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringsuppgift för en högre säkerhetsnivå.

## <a name="client-credentials-grant-flow-diagram"></a>Flödesdiagram för beviljande av klientautentiseringsuppgifter för
Diagrammet nedan förklaras hur med klientens autentiseringsuppgifter bevilja flow fungerar i Azure Active Directory (AD Azure).

![OAuth2.0 bevilja flödet](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientprogrammet autentiserar till Azure AD-slutpunkten för utfärdande och begär en åtkomsttoken.
2. Azure AD-slutpunkten för utfärdande utfärdar en åtkomsttoken.
3. Åtkomsttoken för att autentisera till den skyddade resursen.
4. Data från den skyddade resursen returneras till klientprogrammet.

## <a name="register-the-services-in-azure-ad"></a>Registrera tjänsterna i Azure AD
Registrera både den anropande tjänsten och den mottagande tjänsten i Azure Active Directory (AD Azure). Detaljerade anvisningar finns i [integrera program med Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Begär en åtkomsttoken
Om du vill begära en åtkomsttoken, använder du en HTTP POST till klient-specifika Azure AD-slutpunkten.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Tjänst-till-tjänst begäran om åtkomsttoken
Det finns två fall beroende på om klientprogrammet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Först för användningsfall: begäran om åtkomsttoken med en delad hemlighet
När du använder en delad hemlighet, innehåller en tjänst-till-tjänst begäran om åtkomsttoken följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| _typ av beviljande |obligatorisk |Anger den begärda beviljandetypen. Värdet måste vara i ett flöde för klientautentiseringsuppgifter, **client_credentials**. |
| client_id |obligatorisk |Anger Azure AD-klient-id för uppringande webbtjänsten. Du hittar klient-ID för det anropande programmet gå den [Azure-portalen](https://portal.azure.com), klickar du på **Azure Active Directory**, klickar du på **appregistreringar**, klickar du på programmet. Client_id är den *program-ID* |
| client_secret |obligatorisk |Ange en nyckel som har registrerats för det anropande tjänste- eller daemonappar webbprogrammet i Azure AD. Om du vill skapa en nyckel i Azure portal, klickar du på **Azure Active Directory**, klickar du på **appregistreringar**, klickar du på programmet, klickar du på **inställningar**, klickar du på **nycklar** , och Lägg till en nyckel.  URL-koda det här hemliga när du anger den. |
| resurs |obligatorisk |Ange App-ID-URI för den mottagande webbtjänsten. För att hitta URI: N för App-ID i Azure portal, klickar du på **Azure Active Directory**, klickar du på **appregistreringar**, klickar du på tjänstprogrammet och klicka sedan på **inställningar** och  **Egenskaper för**. |

#### <a name="example"></a>Exempel
Följande HTTP POST begär en åtkomsttoken för den https://service.contoso.com/ webbtjänsten. Den `client_id` identifierar det webbtjänsttillägg som begär åtkomst-token.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: begäran om åtkomsttoken med ett certifikat
En begäran för tjänst-till-tjänst åtkomst-token med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| _typ av beviljande |obligatorisk |Anger den efterfrågade svarstypen. Värdet måste vara i ett flöde för klientautentiseringsuppgifter, **client_credentials**. |
| client_id |obligatorisk |Anger Azure AD-klient-id för uppringande webbtjänsten. Du hittar klient-ID för det anropande programmet gå den [Azure-portalen](https://portal.azure.com), klickar du på **Azure Active Directory**, klickar du på **appregistreringar**, klickar du på programmet. Client_id är den *program-ID* |
| client_assertion_type |obligatorisk |Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obligatorisk | Ett intyg (en JSON Web Token) som du behöver för att skapa och signera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig hur du registrerar ditt certifikat och format för kontrollen.|
| resurs | obligatorisk |Ange App-ID-URI för den mottagande webbtjänsten. För att hitta URI: N för App-ID i Azure portal, klickar du på **Azure Active Directory**, klickar du på **appregistreringar**, klickar du på tjänstprogrammet och klicka sedan på **inställningar** och  **Egenskaper för**. |

Observera att parametrarna är nästan samma sätt som i fallet med begäran från delad hemlighet förutom att parametern client_secret ersätts av två parametrar: en client_assertion_type och client_assertion.

#### <a name="example"></a>Exempel
Följande HTTP POST begär en åtkomsttoken för den https://service.contoso.com/ webbtjänst med ett certifikat. Den `client_id` identifierar det webbtjänsttillägg som begär åtkomst-token.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Tjänst-till-tjänst åtkomst-Token svar

Ett lyckat svar innehåller ett JSON OAuth 2.0-svar med följande parametrar:

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomst-token. Anropa webbtjänsten kan använda denna token för att autentisera till mottagande webbtjänsten. |
| token_type |Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är **ägar**. Mer information om ägar-token finns i den [Framework för OAuth 2.0-auktorisering: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| expires_on |Den tid då den åtkomst-token upphör att gälla. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills de upphör att gälla. Det här värdet används för att fastställa livslängd för cachelagrade token. |
| not_before |Den tid som åtkomsttoken blir kan användas. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills giltighetstiden för token.|
| resurs |App-ID URI för den mottagande webbtjänsten. |

#### <a name="example-of-response"></a>Exempel på svaret
I följande exempel visas ett lyckat svar på en begäran om en åtkomst-token till en webbtjänst.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Se också
* [OAuth 2.0 i Azure AD](v1-protocols-oauth-code.md)
* [Exemplet i C# i tjänst till tjänst-anrop med en delad hemlighet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) och [exemplet i C# i tjänst till tjänst-anrop med ett certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
