---
title: "Azure AD tjänster Auth med OAuth2.0 | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänster autentisering med hjälp av OAuth2.0 bevilja klientautentiseringsuppgifter."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: cc30a54cd56c0cb03a67f86e4552398baa764e58
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# Tjänsten med hjälp av klientens autentiseringsuppgifter (delad hemlighet eller certifikat)-anrop
OAuth 2.0 klientens autentiseringsuppgifter bevilja flöda tillåter en webbtjänst (*konfidentiell klienten*) kan använda sina egna autentiseringsuppgifter i stället för att personifiera en användare för att autentisera när du anropar en annan webbtjänst. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemon tjänst eller webbplats. Azure AD kan även anropa tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en referens för en högre säkerhetsnivå.

## Klientens autentiseringsuppgifter bevilja flödesdiagram
Följande diagram förklarar hur klientens autentiseringsuppgifter bevilja flödet fungerar i Azure Active Directory (AD Azure).

![OAuth2.0 bevilja för klientautentiseringsuppgifter](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Klientprogrammet autentiserar till Azure AD utfärdande slutpunkten och begär en åtkomst-token.
2. Utfärdande-slutpunkten för Azure AD utfärdar åtkomst-token.
3. Åtkomst-token används för att autentisera till den skyddade resursen.
4. Data från den skyddade resursen returneras till webbprogrammet.

## Registrera tjänster i Azure AD
Registrera både anropa tjänsten och den mottagande tjänsten i Azure Active Directory (AD Azure). Detaljerade instruktioner finns [integrera program med Azure Active Directory](active-directory-integrating-applications.md).

## Begär en åtkomst-Token
Om du vill begära en åtkomst-token, använder du en HTTP POST till klient-specifika Azure AD-slutpunkten.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## Begäran för tjänst-till-tjänst åtkomst-token
Det finns två fall beroende på om klientprogrammet väljer att skyddas av en delad hemlighet, eller ett certifikat.

### Först fall: token åtkomst-begäran med en delad hemlighet
När du använder en delad hemlighet, innehåller en tjänst-till-tjänst åtkomst tokenbegäran följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |Krävs |Anger den begärda grant-typen. Värdet måste vara i ett klient autentiseringsuppgifter Grant-flöde **client_credentials**. |
| client_id |Krävs |Anger Azure AD-klient-id för uppringande webbtjänsten. Att hitta det anropande programmet klient-ID i den [Azure-portalen](https://portal.azure.com), klickar du på **Active Directory**, växla directory, klicka på programmet. Client_id är den *program-ID* |
| client_secret |Krävs |Ange en nyckel som registrerats för anropa tjänsten eller daemon webbprogrammet i Azure AD. Klicka för att skapa en nyckel i Azure portal **Active Directory**, växla directory, klicka på programmet, **inställningar**, klickar du på **nycklar**, och lägga till en nyckel.|
| Resursen |Krävs |Ange URI för App-ID för mottagande webbtjänsten. Om du vill hitta URI: N för App-ID i Azure-portalen klickar du på **Active Directory**, växla directory, klicka på tjänstprogrammet och klicka sedan på **inställningar** och **egenskaper** |

#### Exempel
Följande HTTP POST-begäranden en åtkomst-token för https://service.contoso.com/-webbtjänsten. Den `client_id` identifierar det webbtjänsttillägg som begär åtkomst-token.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### Andra fall: token åtkomst-begäran med ett certifikat
En token-tjänster åtkomst-begäran med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |Krävs |Anger den begärda svarstypen. Värdet måste vara i ett klient autentiseringsuppgifter Grant-flöde **client_credentials**. |
| client_id |Krävs |Anger Azure AD-klient-id för uppringande webbtjänsten. Att hitta det anropande programmet klient-ID i den [Azure-portalen](https://portal.azure.com), klickar du på **Active Directory**, växla directory, klicka på programmet. Client_id är den *program-ID* |
| client_assertion_type |Krävs |Värdet måste vara`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Krävs | Ett intyg (en JSON Web Token) som du behöver för att skapa och registrera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig att registrera ditt certifikat och format för kontrollen.|
| Resursen | Krävs |Ange URI för App-ID för mottagande webbtjänsten. Om du vill hitta URI: N för App-ID i Azure-portalen klickar du på **Active Directory**, klickar du på katalogen, klicka på programmet och klicka sedan på **konfigurera**. |

Observera att parametrarna är nästan desamma som i fallet med begäran från delad hemlighet förutom att client_secret-parameter har ersatts av två parametrar: en client_assertion_type och client_assertion.

#### Exempel
Följande HTTP POST-begäranden en åtkomst-token för webbtjänsten https://service.contoso.com/ med ett certifikat. Den `client_id` identifierar det webbtjänsttillägg som begär åtkomst-token.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### Tjänst-till-tjänst åtkomst-Token svar

Ett lyckat svar innehåller ett JSON OAuth 2.0-svar med följande parametrar:

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomst-token. Anropa webbtjänsten kan använda den här variabeln för att autentisera till mottagande webbtjänsten. |
| token_type |Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är **ägar**. Läs mer om ägar-token i [OAuth 2.0 auktorisering Framework: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| expires_on |Tiden då den åtkomst-token upphör att gälla. Representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC tills förfallotid. Det här värdet används för att fastställa livslängden för cachelagrade token. |
| not_before |Den tid som blir den åtkomst-token kan användas. Representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC förrän giltighetstiden för token.|
| Resursen |App-ID URI för mottagande webbtjänsten. |

#### Exempel på svaret
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

## Se även
* [OAuth 2.0 i Azure AD](active-directory-protocols-oauth-code.md)
* [Exemplet i C# för tjänst-till-anrop med en delad hemlighet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) och [exemplet i C# för tjänst-till-anrop med ett certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
