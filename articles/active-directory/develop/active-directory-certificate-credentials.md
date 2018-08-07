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
ms.date: 07/24/2018
ms.author: celested
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7d3796d8d4a5a2e292afaf9cd013ff04ffc082c5
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578678"
---
# <a name="certificate-credentials-for-application-authentication"></a>Autentiseringsuppgifter för certifikat för autentisering för programmet

Azure Active Directory (Azure AD) kan ett program att använda sina egna autentiseringsuppgifter för autentisering, till exempel i flödet för OAuth 2.0-klientautentiseringsuppgifter ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) och den On-Behalf-Of Flow ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

En form av autentiseringsuppgifter som ett program kan använda för autentisering är en JSON Web Token(JWT) försäkran som signeras med ett certifikat som programmet äger.

## <a name="assertion-format"></a>Assertion format
Om du vill beräkna kontrollen, du kan använda en av många [JSON Web Token](https://jwt.ms/) bibliotek i valfritt språk. Den information som utförs av token är följande:

### <a name="header"></a>Sidhuvud

| Parameter |  Kommentar |
| --- | --- |
| `alg` | Bör vara **RS256** |
| `typ` | Bör vara **JWT** |
| `x5t` | Ska vara tumavtrycket för X.509-certifikatet SHA-1 |

### <a name="claims-payload"></a>Anspråk (nyttolast)

| Parameter |  Kommentarer |
| --- | --- |
| `aud` | Målgrupp: Bör vara  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token** |
| `exp` | Förfallodatum: det datum när token upphör att gälla. Tiden representeras som hur många sekunder från 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills giltigheten token upphör att gälla.|
| `iss` | Utgivare: bör vara client_id (program-ID för klienttjänsten) |
| `jti` | GUID: JWT ID |
| `nbf` | Inte före: det datum som token inte kan användas. Tiden representeras som hur många sekunder från 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills token utfärdats. |
| `sub` | Ämne: som för `iss`, ska vara client_id (program-ID för klienttjänsten) |

### <a name="signature"></a>Signatur

Signaturen är beräknad tillämpa certifikatet enligt beskrivningen i den [JSON Web Token RFC7519-specifikation](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exempel på en avkodade JWT-kontrollen

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

## <a name="example-of-an-encoded-jwt-assertion"></a>Exempel på en kodad JWT-kontrollen

Följande sträng är ett exempel på kodade kontrollen. Om du tittar noggrant, kan du se tre avsnitt avgränsade med punkter (.):
* Det första avsnittet kodar rubriken
* Det andra avsnittet kodar nyttolasten
* Det sista avsnittet är signaturen beräknas med certifikat från innehållet i de första två avsnitten

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registrera certifikatet med Azure AD

Du kan associera certifikat-autentiseringsuppgifter med klientprogram i Azure AD via Azure-portalen med hjälp av någon av följande metoder:

### <a name="uploading-the-certificate-file"></a>Överföring av certifikatfilen

I Azure appregistreringen för klientprogrammet:
1. Välj **Inställningar > nycklar** och välj sedan **ladda upp offentlig nyckel**. 
2. Markera den certifikatfil du vill ladda upp.
3. Välj **Spara**. 
   
   När du sparar certifikatet har laddats upp och tumavtryck, startdatum och upphör att gälla värden visas. 

### <a name="updating-the-application-manifest"></a>Uppdatera applikationsmanifestet

Med undantag för ett certifikat, måste du beräkna:

- `$base64Thumbprint`, som är base64-kodning av certifikat-hash
- `$base64Value`, vilket är base64-kodning av rådata för certifikatet

Du måste också ange ett GUID för att identifiera nyckeln i applikationsmanifestet (`$keyId`).

I Azure appregistreringen för klientprogrammet:
1. Öppna programmanifestet.
2. Ersätt den *keyCredentials* egenskap med din nya certifikatinformationen med hjälp av följande schema.

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
3. Spara ändringarna i manifestet och överför sedan manifestet till Azure AD. 

   Den `keyCredentials` egenskap har flera värden, så att du kan ladda upp flera certifikat för bättre hantering av nycklar.
   
## <a name="code-sample"></a>Kodexempel

Kodexempel på [autentiserar till Azure AD i daemon-appar med certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) visar hur ett program använder sina egna autentiseringsuppgifter för autentisering. Det visar även hur du kan [skapa ett självsignerat certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) med hjälp av den `New-SelfSignedCertificate` Powershell-kommando. Du kan också dra nytta och använda den [skripten för att skapa appen](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) att skapa certifikat, compute tumavtrycket och så vidare.
