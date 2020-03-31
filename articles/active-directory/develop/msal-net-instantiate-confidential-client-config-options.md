---
title: Instansiera en konfidentiell klientapp (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du instansierar ett konfidentiellt klientprogram med konfigurationsalternativ med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084729"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instansiera ett konfidentiellt klientprogram med konfigurationsalternativ med MSAL.NET

I den hÃ¤r artikeln beskrivs hur du instansierar ett [konfidentiellt klientprogram](msal-client-applications.md) med Microsoft Authentication Library for .NET (MSAL.NET).  Programmet instansieras med konfigurationsalternativ som definieras i en inställningsfil.

Innan du initierar ett program måste du först [registrera](quickstart-register-app.md) det så att din app kan integreras med Microsofts identitetsplattform. Efter registreringen kan du behöva följande information (som finns i Azure-portalen):

- Klient-ID (en sträng som representerar ett GUID)
- Identitetsleverantörens URL (namngiven instans) och inloggningsmålgruppen för ditt program. Dessa två parametrar kallas gemensamt myndigheten.
- Klient-ID om du skriver en branschprogram endast för din organisation (även namnet single-tenant application).
- Programhemligheten (klienthemlig sträng) eller certifikat (av typen X509Certificate2) om det är en konfidentiell klientapp.
- För webbappar och ibland för offentliga klientappar (särskilt när appen behöver använda en mäklare) har du också angett omdirigeuri där identitetsleverantören kontaktar tillbaka ditt program med säkerhetstoken.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurera programmet från konfigurationsfilen
Namnet på egenskaperna för alternativen i MSAL.NET matchar namnet på egenskaperna `AzureADOptions` för i ASP.NET Core, så du behöver inte skriva någon limkod.

En ASP.NET Core-programkonfiguration beskrivs i en *appsettings.json-fil:*

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Från och med MSAL.NET v3.x kan du konfigurera ditt konfidentiella klientprogram från konfigurationsfilen.

I den klass där du vill konfigurera och instansiera `ConfidentialClientApplicationOptions` programmet måste du deklarera ett objekt.  Bind konfigurationen från källan (inklusive filen appconfig.json) till förekomsten av `IConfigurationRoot.Bind()` programalternativen med hjälp av metoden från [nuget-paketet Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Detta gör att innehållet i avsnittet "AzureAD" i *filen appsettings.json* kan `ConfidentialClientApplicationOptions` bindas till motsvarande egenskaper för objektet.  Skapa sedan `ConfidentialClientApplication` ett objekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Lägga till körningskonfiguration
I ett konfidentiellt klientprogram har du vanligtvis en cache per användare. Därför måste du hämta cacheminnet som är associerat med användaren och informera programverktyget om att du vill använda den. På samma sätt kan du ha en dynamiskt beräknad omdirigera URI. I detta fall koden är följande:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

