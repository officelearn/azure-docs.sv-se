---
title: Migrera OWIN-baserade webb-API:er till b2clogin.com
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar ett .NET-webb-API för att stödja token som utfärdats av flera tokenutfärdare medan du migrerar dina program till b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184102"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrera ett OWIN-baserat webb-API till b2clogin.com

I den här artikeln beskrivs en teknik för att aktivera stöd för flera tokenutfärdare i webb-API:er som implementerar [Det öppna webbgränssnittet för .NET (OWIN)](http://owin.org/). Det är användbart att stödja flera tokenslutpunkter när du migrerar Azure Active Directory B2C-API:er (Azure AD B2C) och deras program från *login.microsoftonline.com* till *b2clogin.com*.

Genom att lägga till stöd i ditt API för att acceptera token som utfärdats av både b2clogin.com och login.microsoftonline.com kan du migrera dina webbprogram i en stegvis sätt innan du tar bort stöd för login.microsoftonline.com utfärdade token från API:et.

I följande avsnitt finns ett exempel på hur du aktiverar flera utfärdare i ett webb-API som använder [Microsoft OWIN][katana] middleware-komponenterna (Katana). Även om kodexemplen är specifika för Microsoft OWIN-mellanprogram, bör den allmänna tekniken vara tillämplig på andra OWIN-bibliotek.

> [!NOTE]
> Den här artikeln är avsedd för Azure AD B2C-kunder `login.microsoftonline.com` med för närvarande distribuerade `b2clogin.com` API:er och program som refererar till och som vill migrera till den rekommenderade slutpunkten. Om du konfigurerar ett nytt program använder [du b2clogin.com](b2clogin.md) enligt anvisningarna.

## <a name="prerequisites"></a>Krav

Du behöver följande Azure AD B2C-resurser på plats innan du fortsätter med stegen i den här artikeln:

* [Användarflöden](tutorial-create-user-flows.md) eller [anpassade principer](custom-policy-get-started.md) som skapats i din klientorganisation

## <a name="get-token-issuer-endpoints"></a>Hämta slutpunkter för tokenutfärdare

Du måste först hämta url:er för tokenutfärdaren för varje utfärdare som du vill stödja i ditt API. Om du vill få *b2clogin.com* och *login.microsoftonline.com* slutpunkter som stöds av din Azure AD B2C-klient använder du följande procedur i Azure-portalen.

Börja med att välja ett av dina befintliga användarflöden:

1. Navigera till din Azure AD B2C-klient i [Azure-portalen](https://portal.azure.com)
1. Under **Principer**väljer du **Användarflöden (principer)**
1. Välj en befintlig princip, till exempel *B2C_1_signupsignin1*och välj sedan **Kör användarflöde**
1. Under rubriken **Kör användarflöde** högst upp på sidan väljer du hyperlänken för att navigera till slutpunkten för OpenID Connect-identifiering för det användarflödet.

    ![Välkänd URI-hyperlänk på sidan Kör nu i Azure-portalen](media/multi-token-endpoints/portal-01-policy-link.png)

1. På sidan som öppnas i webbläsaren `issuer` registrerar du värdet, till exempel:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Använd listrutan **Välj domän** för att välja den andra domänen och `issuer` utför sedan de två föregående stegen igen och registrerar dess värde.

Du bör nu ha två URI:er inspelade som liknar:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Anpassade principer

Om du har anpassade principer i stället för användarflöden kan du använda en liknande process för att hämta utfärdarens URI:er.

1. Navigera till din Azure AD B2C-klient
1. Välj **Ramverk för identitetsupplevelse**
1. Välj en av dina förlitande partiprinciper, till exempel *B2C_1A_signup_signin*
1. Använd listrutan **Välj domän** för att välja en domän, till exempel *yourtenant.b2clogin.com*
1. Markera hyperlänken som visas under **slutpunkten för OpenID Connect-identifiering**
1. Registrera `issuer` värdet
1. Utför stegen 4-6 för den andra domänen, till exempel *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Hämta exempelkoden

Nu när du har båda urodes tokenslutpunkts-URI:er måste du uppdatera koden för att ange att båda slutpunkterna är giltiga utfärdare. Om du vill gå igenom ett exempel hämtar eller klonar du exempelprogrammet och uppdaterar sedan exemplet för att stödja båda slutpunkterna som giltiga utfärdare.

Ladda ner arkivet: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Aktivera flera utfärdare i webb-API

I det här avsnittet uppdaterar du koden för att ange att båda tokenutfärdarens slutpunkter är giltiga.

1. Öppna **lösningen B2C-WebAPI-DotNet.sln** i Visual Studio
1. Öppna *TaskService-filen\\\\App_Start **Startup.Auth.cs*** i redigeraren i **TaskService-projektet**
1. Lägg till `using` följande direktiv överst i filen:

    `using System.Collections.Generic;`
1. Lägg [`ValidIssuers`][validissuers] till egenskapen i [`TokenValidationParameters`][tokenvalidationparameters] definitionen och ange båda URI:erna som du har spelat in i föregående avsnitt:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`tillhandahålls av MSAL.NET och förbrukas av OWIN-mellanprogram i nästa kodavsnitt i *Startup.Auth.cs*. Med flera giltiga utfärdare angivna görs OWIN-programpipelinen medveten om att båda tokenslutpunkterna är giltiga utfärdare.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Som tidigare nämnts tillhandahåller andra OWIN-bibliotek vanligtvis en liknande möjlighet för att stödja flera emittenter. Även om det finns exempel för alla bibliotek utanför den här artikelns omfattning kan du använda en liknande teknik för de flesta bibliotek.

## <a name="switch-endpoints-in-web-app"></a>Växla slutpunkter i webbapp

Med båda URI:er som nu stöds av webb-API:et behöver du nu uppdatera webbprogrammet så att det hämtar token från b2clogin.com slutpunkten.

Du kan till exempel konfigurera exempelwebbprogrammet så att den `ida:AadInstance` nya slutpunkten används genom att ändra värdet i *TaskWebApp\\**Web.config*** i **TaskWebApp-projektet.**

Ändra `ida:AadInstance` värdet i *TaskWebApp:s web.config* så `{your-b2c-tenant-name}.b2clogin.com` att `login.microsoftonline.com`det refererar till i stället för .

Innan:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Efter (ersätt `{your-b2c-tenant}` med namnet på din B2C-klient):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

När slutpunktssträngarna skapas under körningen av webbappen används de b2clogin.com-baserade slutpunkterna när den begär token.

## <a name="next-steps"></a>Nästa steg

I den här artikeln presenterades en metod för att konfigurera ett webb-API som implementerar Microsoft OWIN middleware (Katana) för att acceptera token från flera utfärdarslutpunkter. Som du kanske märker finns det flera andra strängar i *Web.Config-filerna* för både TaskService- och TaskWebApp-projekt som skulle behöva ändras om du vill skapa och köra dessa projekt mot din egen klientorganisation. Du är välkommen att ändra projekten på lämpligt sätt om du vill se dem i aktion, men en fullständig genomgång av detta ligger utanför ramen för den här artikeln.

Mer information om de olika typerna av säkerhetstoken som avges av Azure AD B2C finns [i Översikt över token i Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
