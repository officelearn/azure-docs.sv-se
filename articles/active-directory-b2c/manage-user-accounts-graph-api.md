---
title: Använd Graph API i Azure Active Directory B2C
description: Hantera användare i en Azure AD B2C klient genom att anropa Azure AD-Graph API och använda en program identitet för att automatisera processen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a9e55edcb7c107a3dfa91f61aaa1fea64bc62f21
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848881"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Använd Azure AD-Graph API

Azure Active Directory B2C-klienter (Azure AD B2C) kan ha tusentals eller miljon tals användare. Det innebär att många vanliga klient hanterings uppgifter måste utföras program mässigt. En primärt exempel är användarhantering.

Du kan behöva migrera ett befintligt användar arkiv till en B2C-klient. Du kanske vill vara värd för användarregistrering på sidan och skapa användarkonton i Azure AD B2C-katalogen i bakgrunden. Dessa typer av aktiviteter kräver möjlighet att skapa, läsa, uppdatera och ta bort användar konton. Du kan utföra sådana uppgifter med hjälp av Azure AD-Graph API.

För B2C-klienter finns det två huvudsakliga lägen för att kommunicera med Graph API:

* För **interaktiva**, kör en gång-aktiviteter bör du agera som administratörs konto i B2C-klienten när du utför uppgifterna. För det här läget krävs en administratör för att logga in med autentiseringsuppgifter innan administratören kan utföra anrop till Graph API.
* För **automatiserade**, kontinuerliga uppgifter bör du använda någon typ av tjänst konto som du anger med de behörigheter som krävs för att utföra hanterings uppgifter. I Azure AD kan du göra detta genom att registrera ett program och autentisera till Azure AD. Detta görs med hjälp av ett *program-ID* som använder [OAuth 2,0-klientens autentiseringsuppgifter](../active-directory/develop/service-to-service.md). I det här fallet fungerar programmet som de ska, inte som en användare, för att anropa Graph API.

I den här artikeln får du lära dig hur du utför det automatiska användnings fallet. Du skapar ett .NET 4,5-`B2CGraphClient` som utför åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD). Klienten har ett Windows kommando rads gränssnitt (CLI) som gör att du kan anropa olika metoder. Koden är dock skriven att bete sig i ett icke-interaktivt, automatiserat sätt.

## <a name="prerequisites"></a>Krav

Innan du kan skapa program eller användare behöver du en Azure AD B2C klient. Om du inte redan har en, [skapar du en Azure Active Directory B2C klient](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Registrera ett program

När du har en Azure AD B2C klient måste du registrera ditt hanterings program med hjälp av [Azure Portal](https://portal.azure.com). Du måste också ge den behörighet som krävs för att utföra hanterings uppgifter för ditt automatiserade skript eller hanterings program.

### <a name="register-application-in-azure-active-directory"></a>Registrera program i Azure Active Directory

Om du vill använda Azure AD-Graph API med din B2C-klient måste du registrera ett program med hjälp av arbets flödet Azure Active Directory program registrering.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Tilldela API-åtkomst behörigheter

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Skapa klient hemlighet

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nu har du ett program som har behörighet att *skapa*, *läsa*och *Uppdatera* användare i din Azure AD B2C klient. Fortsätt till nästa avsnitt för att lägga till användar *borttagnings* -och *lösen ords uppdaterings* behörigheter.

## <a name="add-user-delete-and-password-update-permissions"></a>Lägg till användar borttagnings-och lösen ords uppdaterings behörigheter

Behörigheterna *läsa och skriva katalog data* som du har beviljat tidigare inkluderar **inte** möjligheten att ta bort användare eller uppdatera sina lösen ord.

Om du vill ge ditt program möjlighet att ta bort användare eller uppdatera lösen ord, måste du ge rollen som *användar administratör* .

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **Hantera**väljer du **roller och administratörer**.
1. Välj rollen **användar administratör** .
1. Välj **Lägg till tilldelning**.
1. I rutan **Välj** text anger du namnet på det program som du registrerade tidigare, till exempel *managementapp1*. Välj ditt program när det visas i Sök resultatet.
1. Välj **Lägg till**. Det kan ta några minuter innan behörigheterna är fullständigt spridda.

Ditt Azure AD B2C-program har nu ytterligare behörigheter som krävs för att ta bort användare eller uppdatera sina lösen ord i B2C-klienten.

## <a name="get-the-sample-code"></a>Hämta exempelkoden

Kod exemplet är ett .NET-konsol program som använder [Active Directory-autentiseringsbibliotek (ADAL)](../active-directory/develop/active-directory-authentication-libraries.md) för att interagera med Azure AD Graph API. Koden visar hur du anropar API: et för att hantera användare program mässigt i en Azure AD B2C klient organisation.

Du kan [Ladda ned exempel arkivet](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) eller klona GitHub-lagringsplatsen:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

När du har fått kod exemplet konfigurerar du det för din miljö och skapar sedan projektet:

1. Öppna därefter `B2CGraphClient\B2CGraphClient.sln`-lösningen i Visual Studio.
1. Öppna filen *app. config* i **B2CGraphClient** -projektet.
1. Ersätt `<appSettings>`-avsnittet med följande XML. Then replace `{your-b2c-tenant}` with the name of your tenant, and `{Application ID}` and `{Client secret}` with the values you recorded earlier.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Skapa lösningen. Right-click on the **B2CGraphClient** solution in the Solution Explorer, and then select **Rebuild Solution**.

If the build is successful, the `B2C.exe` console application can be found in `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Granska exempelkoden

To use the B2CGraphClient, open a Command Prompt (`cmd.exe`) and change to project's `Debug` directory. Then, run the `B2C Help` command.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

The `B2C Help` command displays a brief description of the available subcommands. Each time you invoke one of its subcommands, `B2CGraphClient` sends a request to the Azure AD Graph API.

The following sections discuss how the application's code makes calls to the Azure AD Graph API.

### <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Any request to the Azure AD Graph API requires an access token for authentication. `B2CGraphClient` uses the open-source Active Directory Authentication Library (ADAL) to assist in obtaining access tokens. ADAL makes token acquisition easier by providing a helper API and taking care of a few important details like caching access tokens. You don't have to use ADAL to get tokens, however. You could instead get tokens by manually crafting HTTP requests.

> [!NOTE]
> You must use ADAL v2 or higher to get access tokens that can be used with the Azure AD Graph API. You cannot use ADAL v1.

When `B2CGraphClient` executes, it creates an instance of the `B2CGraphClient` class. The constructor for this class sets up the ADAL authentication scaffolding:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Let's use the `B2C Get-User` command as an example.

When `B2C Get-User` is invoked without additional arguments, the application calls the `B2CGraphClient.GetAllUsers()` method. `GetAllUsers()` then calls `B2CGraphClient.SendGraphGetRequest()`, which submits an HTTP GET request to the Azure AD Graph API. Before `B2CGraphClient.SendGraphGetRequest()` sends the GET request, it first obtains an access token by using ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

You can get an access token for the Graph API by calling the ADAL `AuthenticationContext.AcquireToken()` method. ADAL then returns an `access_token` that represents the application's identity.

### <a name="read-users"></a>Read users

When you want to get a list of users or get a particular user from the Azure AD Graph API, you can send an HTTP `GET` request to the `/users` endpoint. A request for all of the users in a tenant looks like this:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

To see this request, run:

 ```cmd
 B2C Get-User
 ```

There are two important things to note:

* The access token obtained by using ADAL is added to the `Authorization` header by using the `Bearer` scheme.
* For B2C tenants, you must use the query parameter `api-version=1.6`.

Both of these details are handled in the `B2CGraphClient.SendGraphGetRequest()` method:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Create consumer user accounts

When you create user accounts in your B2C tenant, you can send an HTTP `POST` request to the `/users` endpoint. The following HTTP `POST` request shows an example user to be created in the tenant.

Most of properties in the following request are required to create consumer users. The `//` comments have been included for illustration--do not include them in an actual request.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

To see the request, run one of the following commands:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

The `Create-User` command takes as an input parameter a JSON file that contains a JSON representation of a user object. Det finns två exempel-JSON-filer i kod exemplet: `usertemplate-email.json` och `usertemplate-username.json`. Du kan ändra filerna så att de passar dina behov. Förutom de obligatoriska fälten ovan ingår flera valfria fält i filerna.

Mer information om obligatoriska och valfria fält finns i referens för [entiteter och komplex typ | Graph API referens](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Du kan se hur POST-begäran konstrueras i `B2CGraphClient.SendGraphPostRequest()`:

* Den bifogar en åtkomsttoken till `Authorization` rubriken för begäran.
* Den anger `api-version=1.6`.
* Den inkluderar JSON-användarobjektet i bröd texten i begäran.

> [!NOTE]
> Om de konton som du vill migrera från ett befintligt användar Arkiv har en lägre lösen ords styrka än den [starka lösen ords styrkan som tillämpas av Azure AD B2C](user-flow-password-complexity.md), kan du inaktivera kravet på starkt lösen ord genom att använda `DisableStrongPassword`-värdet i `passwordPolicies`-egenskapen. Du kan till exempel ändra föregående begäran om att skapa användare på följande sätt: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Uppdatera konsument användar konton

När du uppdaterar användar objekt liknar processen den som du använder för att skapa användar objekt, men använder HTTP-`PATCH` metoden:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Försök att uppdatera en användare genom att ändra några värden i dina JSON-filer och Använd sedan `B2CGraphClient` för att köra något av följande kommandon:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Granska `B2CGraphClient.SendGraphPatchRequest()`-metoden för information om hur du skickar den här begäran.

### <a name="search-users"></a>Söka efter användare

Du kan söka efter användare i B2C-klienten på följande sätt:

* Referera till användarens **objekt-ID**.
* Referera till sina inloggnings identifierare, egenskapen `signInNames`.
* Referera till någon av de giltiga OData-parametrarna. Till exempel "givenName", "efter namn", "displayName" osv.

Kör något av följande kommandon för att söka efter en användare:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Ett exempel:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare använder du HTTP-`DELETE` metoden och skapar URL: en med användarens objekt-ID:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Om du vill se ett exempel, anger du det här kommandot och visar borttagnings förfrågan som skrivs ut till-konsolen:

```cmd
B2C Delete-User <object-id-of-user>
```

Granska `B2CGraphClient.SendGraphDeleteRequest()`-metoden för information om hur du skickar den här begäran.

Du kan utföra många andra åtgärder med Azure AD Graph API utöver användar hantering. [Azure AD Graph API-referensen](/previous-versions/azure/ad/graph/api/api-catalog) innehåller information om varje åtgärd, tillsammans med exempel begär Anden.

## <a name="use-custom-attributes"></a>Använd anpassade attribut

De flesta konsument program behöver lagra viss typ av anpassad användar profil information. Ett sätt som du kan göra är att definiera ett anpassat attribut i B2C-klienten. Du kan sedan behandla attributet på samma sätt som du hanterar andra egenskaper för ett användar objekt. Du kan uppdatera attributet, ta bort attributet, fråga efter attributet, skicka attributet som ett anspråk i inloggnings-token med mera.

Information om hur du definierar ett anpassat attribut i B2C-klienten finns i [B2C-referens för anpassade attribut](user-flow-custom-attributes.md).

Du kan visa de anpassade attribut som definierats i din B2C-klient med hjälp av följande `B2CGraphClient`-kommandon:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Utdata visar information om varje anpassat attribut. Ett exempel:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Du kan använda det fullständiga namnet, till exempel `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, som en egenskap för dina användar objekt. Uppdatera din JSON-fil med den nya egenskapen och ett värde för egenskapen och kör sedan:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Nästa steg

Genom att använda `B2CGraphClient`har du ett tjänst program som kan hantera B2C-klientens användare program mässigt. `B2CGraphClient` använder sin egen program identitet för att autentisera till Azure AD-Graph API. Den hämtar också token med hjälp av en klient hemlighet.

När du införlivar den här funktionen i ditt eget program ska du komma ihåg några viktiga punkter för B2C-program:

* Ge programmet de behörigheter som krävs för klienten.
* Använd `api-version=1.6`när du anropar Graph API.
* När du skapar och uppdaterar konsument användare krävs några egenskaper, enligt beskrivningen ovan.
