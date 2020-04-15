---
title: Autentiseringsuppgifter för Microsoft-identitetsplattformscertifikat
titleSuffix: Microsoft identity platform
description: I den här artikeln beskrivs registrering och användning av certifikatautentisering för programautentisering.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d834d37f686279facf0052e3c8f4da3c8d259e0b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309681"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Autentiseringscertifikat för Microsoft-identitetsplattformsprogram

Microsoft identity-plattformen gör det möjligt för ett program att använda sina egna autentiseringsuppgifter för autentisering, till exempel i [OAuth 2.0-klientautentiseringsuppgifterna Grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) och [flödet On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md)).

En form av autentiseringsuppgifter som ett program kan använda för autentisering är ett JSON Web Token(JWT) påstående som undertecknats med ett certifikat som programmet äger.

## <a name="assertion-format"></a>Kontrollformat
Microsoft identity-plattform För att beräkna påståendet kan du använda ett av de många [JSON-webbtokenbiblioteken](https://jwt.ms/) på det språk du väljer. Den information som bärs av token är följande:

### <a name="header"></a>Huvud

| Parameter |  Anmärkning |
| --- | --- |
| `alg` | Bör vara **RS256** |
| `typ` | Bör **JWT** |
| `x5t` | Bör vara X.509 Certificate SHA-1 tumavtryck |

### <a name="claims-payload"></a>Fordringar (nyttolast)

| Parameter |  Anmärkningar |
| --- | --- |
| `aud` | Målgrupp: Bör ** https://login.microsoftonline.com/ *vara tenant_Id*/oauth2/token** |
| `exp` | Utgångsdatum: det datum då token upphör att gälla. Tiden representeras som antalet sekunder från 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills tokengiltigheten upphör att gälla.|
| `iss` | Utfärdare: bör vara client_id (klienttjänstens program-ID) |
| `jti` | GUID: JWT-ID |
| `nbf` | Inte före: det datum före vilket token inte kan användas. Tiden representeras som antalet sekunder från 1 januari 1970 (1970-01-01T0:0:0Z) UTC tills den tidpunkt då token utfärdades. |
| `sub` | Angående: `iss`När det gäller , bör vara client_id (Program-ID för klienttjänsten) |

### <a name="signature"></a>Signatur

Signaturen beräknas tillämpa certifikatet enligt beskrivningen i [JSON Web Token RFC7519-specifikationen](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exempel på ett avkodat JWT-påstående

```JSON
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

## <a name="example-of-an-encoded-jwt-assertion"></a>Exempel på ett kodat JWT-påstående

Följande sträng är ett exempel på kodat påstående. Om du tittar noga, märker du tre avsnitt åtskilda av punkter (.):
* Det första avsnittet kodar huvudet
* Det andra avsnittet kodar nyttolasten
* Det sista avsnittet är signaturen som beräknas med certifikaten från innehållet i de två första avsnitten

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrera certifikatet med Microsofts identitetsplattform

Du kan associera certifikatautentiseringsuppgifterna med klientprogrammet i Microsofts identitetsplattform via Azure-portalen med någon av följande metoder:

### <a name="uploading-the-certificate-file"></a>Ladda upp certifikatfilen

I Azure-appregistreringen för klientprogrammet:
1. Välj **Certifikat & hemligheter**.
2. Klicka på **Ladda upp certifikat** och välj certifikatfilen att ladda upp.
3. Klicka på **Lägg till**.
  När certifikatet har överförts visas tumavtrycket, startdatumet och utgångsdatumvärdena.

### <a name="updating-the-application-manifest"></a>Uppdatera programmanifestet

Om du har ett certifikat måste du beräkna:

- `$base64Thumbprint`, som är bas64-kodningen av certifikatet hash
- `$base64Value`, som är bas64-kodningen av certifikatets rådata

Du måste också ange ett GUID för att`$keyId`identifiera nyckeln i programmanifestet ( ).

I Azure-appregistreringen för klientprogrammet:
1. Välj **Manifest om** du vill öppna programmanifestet.
2. Ersätt egenskapen *keyCredentials* med den nya certifikatinformationen med hjälp av följande schema.

   ```JSON
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
3. Spara ändringarna i programmanifestet och ladda sedan upp manifestet till Microsofts identitetsplattform.

   Egenskapen `keyCredentials` har ett flervärde, så du kan ladda upp flera certifikat för rikare nyckelhantering.

## <a name="code-sample"></a>Kodexempel

> [!NOTE]
> Du måste beräkna X5T-huvudet genom att konvertera det till en bas 64-sträng med hjälp av certifikatets hash. Koden för att utföra detta `System.Convert.ToBase64String(cert.GetCertHash());`i C# är .

[Kodexemplet .NET Core daemon console application using Microsoft identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) visar hur ett program använder sina egna autentiseringsuppgifter för autentisering. Den visar också hur du kan [skapa ett självsignerat certifikat](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) med kommandot `New-SelfSignedCertificate` Powershell. Du kan också dra nytta av och använda skript för [att skapa appar](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) för att skapa certifikat, beräkna tumavtrycket och så vidare.
