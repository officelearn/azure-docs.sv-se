---
title: Certifikat autentiseringsuppgifter i Azure AD | Microsoft Docs
description: Den här artikeln beskrivs registrering och användning av autentiseringsuppgifter för certifikat för autentisering
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c782429ac2d8ee030ca8b589b4241242c7b101d6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156508"
---
# <a name="certificate-credentials-for-application-authentication"></a>Autentiseringsuppgifter för certifikat för autentisering

Azure Active Directory kan ett program att använda sina egna autentiseringsuppgifter för autentisering. Till exempel i flödet för OAuth 2.0 klientens autentiseringsuppgifter Grant ([v1](active-directory-protocols-oauth-service-to-service.md), [v2](active-directory-v2-protocols-oauth-client-creds.md)) och On-Behalf-Of-flöde ([v1](active-directory-protocols-oauth-on-behalf-of.md), [v2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
En form av autentiseringsuppgifter som kan användas är en JSON Web Token(JWT) assertion signeras med ett certifikat som programmet äger.

## <a name="format-of-the-assertion"></a>Formatet på kontrollen
Om du vill beräkna kontrollen, vill du förmodligen använda en av många [JSON Web Token](https://jwt.ms/) bibliotek på önskat språk. Informationen som token är:

#### <a name="header"></a>Sidhuvud

| Parameter |  Kommentar |
| --- | --- |
| `alg` | Bör vara **RS256** |
| `typ` | Bör vara **JWT** |
| `x5t` | Ska vara tumavtrycket för X.509-certifikat SHA-1 |

#### <a name="claims-payload"></a>Anspråk (Payload)

| Parameter |  Kommentar |
| --- | --- |
| `aud` | Målgrupp: Bör vara  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token** |
| `exp` | Förfallodatum: det datum då token upphör att gälla. Tiden representeras som antalet sekunder från den 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills giltigheten token upphör att gälla.|
| `iss` | Utgivare: bör vara client_id (program-ID för klient-tjänst) |
| `jti` | GUID: JWT ID |
| `nbf` | Inte före: datum före vilken token inte kan användas. Tiden representeras som antalet sekunder från den 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills token har utfärdats. |
| `sub` | Ämne: som för `iss`, bör vara client_id (program-ID för klient-tjänst) |

#### <a name="signature"></a>Signatur

Signaturen beräknas tillämpa certifikatet som beskrivs i den [JSON Web Token RFC7519 specifikation](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Exempel på en avkodade JWT-kontrollen

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

### <a name="example-of-an-encoded-jwt-assertion"></a>Exempel på en kodad JWT-kontrollen

Följande sträng är ett exempel på kodade kontrollen. Om du ser noggrant kan se du tre avsnitt avgränsade med punkter (.).
Det första avsnittet kodar rubriken, den andra nyttolasten och sist är signaturen som beräknats med certifikat från innehållet i de två första avsnitten.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Registrera ditt certifikat med Azure AD

Du kan associera certifikat-autentiseringsuppgifter med klientprogrammet i Azure AD via Azure-portalen med hjälp av följande metoder:

**Överföring av certifikatfilen**

I Azure-app-registrering för klientprogrammet klickar du på **inställningar**, klickar du på **nycklar** och klicka sedan på **överför offentliga nyckel**. Välj den certifikatfil du vill överföra och klicka på **spara**. När du sparar certifikatet som överförs och certifikatets tumavtryck startdatum och upphör att gälla visas värden. 

**Uppdatera programmanifestet**

Med undantag för ett certifikat, måste du beräkna:

- `$base64Thumbprint`, vilket är base64-kodning av certifikat-Hash
- `$base64Value`, vilket är base64-kodning av certifikatets rådata

Du måste också ange ett GUID för att identifiera nyckeln i programmanifestet (`$keyId`).

Öppna programmanifestet i Azure-app-registrering för klientprogrammet och Ersätt den *keyCredentials* egenskap med information för din nya certifikat med hjälp av följande schema:

```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Spara ändringarna i programmanifestet och ladda upp till Azure AD. Egenskapen keyCredentials är ett flervärdesattribut, så du kan ladda upp flera certifikat bättre hantering av nycklar.
