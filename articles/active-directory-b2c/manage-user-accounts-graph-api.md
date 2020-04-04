---
title: Hantera användare med Microsoft Graph API
titleSuffix: Azure AD B2C
description: Hantera användare i en Azure AD B2C-klient genom att anropa Microsoft Graph API och använda en programidentitet för att automatisera processen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637330"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Hantera Azure AD B2C-användarkonton med Microsoft Graph

Med Microsoft Graph kan du hantera användarkonton i din Azure AD B2C-katalog genom att tillhandahålla metoder för att skapa, läsa, uppdatera och ta bort i Microsoft Graph API. Du kan migrera ett befintligt användararkiv till en Azure AD B2C-klientorganisation och utföra andra hanteringsåtgärder för användarkonton genom att anropa Microsoft Graph API.

I de avsnitt som följer presenteras de viktigaste aspekterna av Azure AD B2C-användarhantering med Microsoft Graph API. Microsoft Graph API-åtgärder, typer och egenskaper som presenteras här är en delmängd av dem som visas i referensdokumentationen för Microsoft Graph API.

## <a name="register-a-management-application"></a>Registrera ett hanteringsprogram

Innan ett användarhanteringsprogram eller skript som du skriver kan interagera med resurserna i din Azure AD B2C-klient behöver du en programregistrering som ger behörigheterna att göra det.

Följ stegen i den här artikeln om du vill skapa en programregistrering som hanteringsprogrammet kan använda:

[Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph-åtgärder för användarhantering

Följande användarhanteringsåtgärder är tillgängliga i [Microsoft Graph API:](https://docs.microsoft.com/graph/api/resources/user)

- [Hämta en lista över användare](https://docs.microsoft.com/graph/api/user-list)
- [Skapa en användare](https://docs.microsoft.com/graph/api/user-post-users)
- [Skaffa en användare](https://docs.microsoft.com/graph/api/user-get)
- [Uppdatera en användare](https://docs.microsoft.com/graph/api/user-update)
- [Ta bort en användare](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Användaregenskaper

### <a name="display-name-property"></a>Egenskapen Visa namn

Det `displayName` är namnet som ska visas i Azure Portal-användarhantering för användaren och i åtkomsttoken Azure AD B2C returnerar till programmet. Boendet är obligatoriskt.

### <a name="identities-property"></a>Egenskapen Identiteter

Ett kundkonto, som kan vara en konsument, partner eller medborgare, kan associeras med dessa identitetstyper:

- **Lokal** identitet - Användarnamnet och lösenordet lagras lokalt i Azure AD B2C-katalogen. Vi kallar ofta dessa identiteter för "lokala konton".
- **Federerad** identitet – Även ett *socialt* konton eller *företagskonton* hanteras användarens identitet av en federerad identitetsleverantör som Facebook, Microsoft, ADFS eller Salesforce.

En användare med ett kundkonto kan logga in med flera identiteter. Användarnamn, e-post, medarbetar-ID, myndighets-ID och andra. Ett enda konto kan ha flera identiteter, både lokala och sociala, med samma lösenord.

I Microsoft Graph API lagras både lokala och federerade `identities` identiteter i användarattributet, som är av typen [objectIdentity][graph-objectIdentity]. Samlingen `identities` representerar en uppsättning identiteter som används för att logga in på ett användarkonto. Med den här samlingen kan användaren logga in på användarkontot med någon av dess associerade identiteter.

| Egenskap   | Typ |Beskrivning|
|:---------------|:--------|:----------|
|signInType|sträng| Anger typerna för användarloggning i katalogen. För lokalt `emailAddress`konto: `emailAddress2` `emailAddress3`, `userName` `emailAddress1`, , , eller någon annan typ som du gillar. Socialt konto måste `federated`ställas in på .|
|utfärdare|sträng|Anger utfärdaren av identiteten. För lokala konton (där **signInType** inte `federated`är ) är den här egenskapen det lokala B2C-klientstandarddomännamnet, till exempel `contoso.onmicrosoft.com`. För social identitet (där `federated` **signInType** är) är värdet namnet på utfärdaren, till exempel`facebook.com`|
|emittentTilldeladeId|sträng|Anger den unika identifierare som tilldelats användaren av utfärdaren. Kombinationen av **utfärdare** och **utfärdareTilldeladeId** måste vara unik i din klientorganisation. För lokalt konto, när **signInType** är inställt på `emailAddress` eller `userName`representerar det inloggningsnamnet för användaren.<br>När **signInType** är inställt på: <ul><li>`emailAddress`(eller börjar `emailAddress` `emailAddress1`med liknande) **utfärdareTilldeladeId** måste vara en giltig e-postadress</li><li>`userName`(eller något annat värde) måste **utfärdareTilldeladeId** vara en giltig [lokal del av en e-postadress](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`representerar **utfärdarTilldeladeId** den unika identifieraren för federerade kontot</li></ul>|

Följande **identitetsegenskap,** med en lokal kontoidentitet med inloggningsnamn, en e-postadress som inloggning och med en social identitet. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

För federerade identiteter, beroende på identitetsprovidern, är **utfärdarenAssignedId** ett unikt värde för en viss användare per program eller utvecklingskonto. Konfigurera Azure AD B2C-principen med samma program-ID som tidigare tilldelats av den sociala providern eller ett annat program inom samma utvecklingskonto.

### <a name="password-profile-property"></a>Egenskapen Lösenordsprofil

För en lokal identitet krävs **egenskapen passwordProfile** och innehåller användarens lösenord. Egenskapen `forceChangePasswordNextSignIn` måste `false`anges till .

För en federerad (social) identitet krävs inte **egenskapen passwordProfile.**

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Egenskapen Lösenordsprincip

Lösenordsprincipen för Azure AD B2C (för lokala konton) baseras på principen för azure Active [Directory-stark lösenordsstyrka.](../active-directory/authentication/concept-sspr-policy.md) Azure AD B2C-registrerings- eller inloggnings- och lösenordsåterställningsprinciperna kräver den här starka lösenordsstyrkan och upphör inte att gälla lösenord.

I scenarier för användarmigrering, om de konton som du vill migrera har svagare lösenordsstyrka än den [starka lösenordsstyrka](../active-directory/authentication/concept-sspr-policy.md) som tillämpas av Azure AD B2C, kan du inaktivera det starka lösenordskravet. Om du vill ändra standardlösenordsprincipen anger du egenskapen `passwordPolicies` till `DisableStrongPassword`. Du kan till exempel ändra användarbegäran på följande sätt:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Egenskaper för tillägg

Varje kundinriktad applikation har unika krav på att informationen ska samlas in. Din Azure AD B2C-klient levereras med en inbyggd uppsättning information som lagras i egenskaper, till exempel förnamn, efternamn, ort och postnummer. Med Azure AD B2C kan du utöka uppsättningen egenskaper som lagras i varje kundkonto. Mer information om hur du definierar anpassade attribut finns i [anpassade attribut (användarflöden)](user-flow-custom-attributes.md) och [anpassade attribut (anpassade principer).](custom-policy-custom-attributes.md)

Microsoft Graph API stöder att skapa och uppdatera en användare med tilläggsattribut. Tilläggsattribut i Graph API namnges `extension_ApplicationObjectID_attributename`med hjälp av konventionen . Ett exempel:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Kodexempel

Det här kodexemplet är ett .NET Core-konsolprogram som använder [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) för att interagera med Microsoft Graph API. Dess kod visar hur du anropar API:et för att programmässigt hantera användare i en Azure AD B2C-klientorganisation.
Du kan [hämta exempelarkivet](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [bläddra i databasen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) på GitHub eller klona databasen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

När du har fått kodexemplet konfigurerar du det för din miljö och skapar sedan projektet:

1. Öppna projektet i [Visual Studio](https://visualstudio.microsoft.com) eller Visual [Studio Code](https://code.visualstudio.com).
1. Öppna `src/appsettings.json`.
1. I `appSettings` avsnittet ersätter `your-b2c-tenant` du med namnet `Application (client) ID` på `Client secret` din klient och och med värdena för registreringen av hanteringsprogrammet (se avsnittet [Registrera en hanteringsapplikation i](#register-a-management-application) den här artikeln).
1. Öppna ett konsolfönster i din lokala klon av `src` repo, växla till katalogen och bygg sedan projektet:
    ```console
    cd src
    dotnet build
    ```
1. Kör programmet med `dotnet` kommandot:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

Programmet visar en lista över kommandon som du kan köra. Hämta till exempel alla användare, hämta en enskild användare, ta bort en användare, uppdatera en användares lösenord och massimport.

### <a name="code-discussion"></a>Kod diskussion

Exempelkoden använder [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), som är utformat för att förenkla byggandet av högkvalitativa, effektiva och elastiska program som får åtkomst till Microsoft Graph.

Alla förfrågningar till Microsoft Graph API kräver en åtkomsttoken för autentisering. Lösningen använder sig av [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet-paketet som tillhandahåller ett autentiseringsscenariobaserat omslag av Microsoft Authentication Library (MSAL) för användning med Microsoft Graph SDK.

Metoden `RunAsync` i _Program.cs-filen:_

1. Läser programinställningar från _filen appsettings.json_
1. Initierar auth-providern med [OAuth 2.0-klientautentiseringsbidragsflödet.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Med tilldelningsflödet för klientuppgifter kan appen hämta en åtkomsttoken för att anropa Microsoft Graph API.
1. Konfigurerar Microsoft Graph-tjänstklienten med auth-providern:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Den initierade *GraphServiceClient* används sedan i _UserService.cs_ för att utföra användarhanteringsåtgärder. Till exempel att få en lista över användarkonton i klienten:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Ringa API-anrop med hjälp av Microsoft Graph SDK:er](https://docs.microsoft.com/graph/sdks/create-requests) `$select` innehåller information om hur du läser och skriver `$filter` `$orderBy` information från Microsoft Graph, använder för att styra de egenskaper som returneras, tillhandahålla anpassade frågeparametrar och använda parametrarna och fråga.

## <a name="next-steps"></a>Nästa steg

Ett fullständigt index över Microsoft Graph API-åtgärder som stöds för Azure AD B2C-resurser finns i [Microsoft Graph-åtgärder som är tillgängliga för Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
