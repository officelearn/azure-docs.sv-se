---
title: Använd Graph API i Azure Active Directory B2C | Microsoft Docs
description: Så här anropar du Graph API för en B2C-klient genom att använda en program identitet för att automatisera processen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 11a9fc521a7b17ae0ff2f579f173f4d43383bdd5
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880085"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Använd Azure AD Graph API:n

>[!NOTE]
> Du måste använda [Azure AD-Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) för att hantera användare i en Azure AD B2C katalog. Detta skiljer sig från Microsoft Graph-API: et. Lär dig mer [här](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Azure Active Directory (Azure AD) B2C-klienter tenderar att vara mycket stora. Det innebär att många vanliga klient hanterings uppgifter måste utföras program mässigt. En primärt exempel är användarhantering. Du kan behöva migrera ett befintligt användar arkiv till en B2C-klient. Du kanske vill vara värd för användarregistrering på sidan och skapa användarkonton i Azure AD B2C-katalogen i bakgrunden. Dessa typer av uppgifter kräver möjligheten att skapa, läsa, uppdatera och ta bort användarkonton. Du kan utföra dessa uppgifter med hjälp av Azure AD Graph API.

För B2C-klienter finns det två huvudsakliga lägen för att kommunicera med Graph API.

* För interaktiva, kör en gång-aktiviteter bör du agera som administratörs konto i B2C-klienten när du utför uppgifterna. För det här läget krävs en administratör för att logga in med autentiseringsuppgifter innan administratören kan utföra anrop till Graph API.
* För automatiserade, kontinuerliga uppgifter bör du använda någon typ av tjänst konto som du anger med de behörigheter som krävs för att utföra hanterings uppgifter. I Azure AD kan du göra detta genom att registrera ett program och autentisera till Azure AD. Detta görs med hjälp av ett **program-ID** som använder [OAuth 2,0-klientens autentiseringsuppgifter](../active-directory/develop/service-to-service.md). I det här fallet fungerar programmet som de ska, inte som en användare, för att anropa Graph API.

I den här artikeln får du lära dig hur du utför det automatiserade användnings fallet. Du skapar en .NET 4,5 `B2CGraphClient` som utför åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD). Klienten har ett Windows kommando rads gränssnitt (CLI) som gör att du kan anropa olika metoder. Koden är dock skriven att bete sig i ett inaktivt och automatiserat sätt.

## <a name="get-an-azure-ad-b2c-tenant"></a>Hämta en Azure AD B2C klient
Innan du kan skapa program eller användare behöver du en Azure AD B2C klient. [Kom igång med Azure AD B2C](active-directory-b2c-get-started.md)om du inte redan har en klient.

## <a name="register-your-application-in-your-tenant"></a>Registrera ditt program i din klient organisation
När du har en B2C-klient måste du registrera ditt program med hjälp av [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Om du vill använda Graph API med B2C-klienten måste du registrera ett program med hjälp av *app Registration* -tjänsten på menyn Azure Portal, **inte** på Azure AD B2C's- *program* . Följande anvisningar leder dig till lämplig meny. Du kan inte återanvända befintliga B2C-program som du har registrerat på menyn för Azure AD B2C's- *program* .

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj din Azure AD B2C klient genom att välja ditt konto i det övre högra hörnet på sidan.
3. I det vänstra navigerings fönstret väljer du **alla tjänster**, klickar på **program registreringar**och klickar sedan på **ny registrering**.
4. Följ anvisningarna och skapa ett nytt program. 
    1. Lägg till ett lämpligt namn
    2. Välj **konton endast i den här organisations katalogen**
    3. Välj **webb** som program typ och ange **en inloggnings-URL** (t. ex `https://B2CGraphAPI`.) eftersom den inte är relevant för det här exemplet.  
    4. Klicka på registrera.
5. Programmet visas nu i listan med program. Klicka på det för att hämta **program-ID: t** (även kallat klient-ID). Kopiera den som du behöver i ett senare avsnitt.
6. I menyn Inställningar klickar du på **certifikat & hemligheter**.
7. I avsnittet **klient hemligheter** klickar du på **ny klient hemlighet**, anger en beskrivning av hemligheten och väljer en varaktighet och klickar sedan på **Lägg till**. Kopiera värdet för hemligheten (kallas även klient hemlighet) för användning i ett senare avsnitt.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurera skapa, läsa och uppdatera behörigheter för ditt program
Nu måste du konfigurera ditt program så att det får alla behörigheter som krävs för att skapa, läsa, uppdatera och ta bort användare.

1. Fortsätt till menyn Azure Portal appens registrerings program och välj ditt program.
2. I menyn Inställningar klickar du på **behörigheter som krävs**.
3. I menyn nödvändiga behörigheter klickar du på **Windows-Azure Active Directory**.
4. I menyn aktivera åtkomst väljer du behörigheten **läsa och skriva katalog data** från **program behörigheter** och klickar på **Spara**.
5. Slutligen klickar du på knappen **bevilja behörigheter** på menyn behörigheter som krävs.

Nu har du ett program som har behörighet att skapa, läsa och uppdatera användare från din B2C-klient.

> [!NOTE]
> Det kan ta några minuter att bevilja behörigheter att bearbetas fullständigt.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Konfigurera ta bort eller uppdatera lösen ords behörigheter för ditt program
För närvarande inkluderar behörigheten *läsa och skriva katalog data* **inte** möjligheten att ta bort användare eller uppdatera användar lösen ord. Om du vill ge ditt program möjlighet att ta bort användare eller uppdatera lösen ord måste du göra dessa extra steg som inbegriper PowerShell, annars kan du gå vidare till nästa avsnitt.

Först, om du inte redan har installerat, installerar du [Azure AD PowerShell v1-modulen (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

När du har installerat PowerShell-modulen ansluter du till Azure AD B2C-klienten.

> [!IMPORTANT]
> Du måste använda ett B2C klient administratörs konto som är **lokalt** för B2C-klienten. Dessa konton ser ut så här myusername@myb2ctenant.onmicrosoft.com:.

```powershell
Connect-MsolService
```

Nu ska vi använda **program-ID: t** i skriptet nedan för att tilldela programmet rollen som användar konto administratör. De här rollerna har välkända identifierare, så allt du behöver göra är att ange ditt **program-ID** i skriptet nedan.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Ditt program har nu också behörighet att ta bort användare eller uppdatera lösen ord från B2C-klienten.

## <a name="download-configure-and-build-the-sample-code"></a>Ladda ned, konfigurera och bygg exempel koden
Börja med att ladda ned exempel koden och kom igång. Sedan tar vi en närmare titt på den.  Du kan [Ladda ned exempel koden som en. zip-fil](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Du kan också klona den till en valfri katalog:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

`B2CGraphClient\B2CGraphClient.sln` Öppna Visual Studio-lösningen i Visual Studio. Öppna filen`App.config`i projektet. `B2CGraphClient` Ersätt de tre app-inställningarna med dina egna värden:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Högerklicka sedan på `B2CGraphClient` lösningen och återskapa exemplet. Om du lyckas bör du nu ha en `B2C.exe` körbar fil som finns i. `B2CGraphClient\bin\Debug`

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Bygg användar CRUD åtgärder med hjälp av Graph API
Om du vill använda B2CGraphClient öppnar du `cmd` en kommando tolk i Windows och ändrar katalogen `Debug` till katalogen. Kör `B2C Help` sedan kommandot.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Då visas en kort beskrivning av varje kommando. Varje gången du anropar något av dessa kommandon `B2CGraphClient` , skickar en begäran till Azure AD-Graph API.

### <a name="get-an-access-token"></a>Hämta en åtkomsttoken
Alla begär anden till Graph API kräver en åtkomsttoken för autentisering. `B2CGraphClient`använder Active Directory-autentiseringsbibliotek med öppen källkod (ADAL) för att få åtkomst till tokens. ADAL gör token-förvärv enklare genom att tillhandahålla ett enkelt API och ta hand om viktig information, till exempel cachelagring av åtkomst-token. Du behöver inte använda ADAL för att hämta tokens, men. Du kan också hämta token genom att utforma HTTP-begäranden.

> [!NOTE]
> I det här kod exemplet används ADAL v2 för att kommunicera med Graph API.  Du måste använda ADAL v2 eller v3 för att få åtkomsttoken som kan användas med Azure AD-Graph API.
> 
> 

När `B2CGraphClient` körs skapas en instans `B2CGraphClient` av klassen. Konstruktorn för den här klassen ställer in en ADAL-autentiserings-ramverk:

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

Vi använder `B2C Get-User` kommandot som exempel. När `B2C Get-User` anropas utan ytterligare indata `B2CGraphClient.GetAllUsers(...)` anrop anropar CLI-metoden. Den här metoden `B2CGraphClient.SendGraphGetRequest(...)`anropar, som skickar en HTTP GET-begäran till Graph API. Innan `B2CGraphClient.SendGraphGetRequest(...)` skickar GET-begäran får den först en åtkomsttoken med hjälp av ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Du kan hämta en åtkomsttoken för Graph API genom att anropa metoden ADAL `AuthenticationContext.AcquireToken(...)` . ADAL returnerar sedan en `access_token` som representerar programmets identitet.

### <a name="read-users"></a>Läsa användare
När du vill hämta en lista över användare eller hämta en viss användare från Graph API kan du skicka en http- `GET` begäran `/users` till slut punkten. En begäran om alla användare i en klient organisation ser ut så här:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Kör följande om du vill se den här begäran:

 ```cmd
 B2C Get-User
 ```

Det finns två viktiga saker att tänka på:

* Den åtkomsttoken som förvärvas via ADAL läggs till `Authorization` i rubriken med `Bearer` hjälp av schemat.
* Du måste använda Frågeparametern `api-version=1.6`för B2C-klienter.

Båda dessa uppgifter hanteras i `B2CGraphClient.SendGraphGetRequest(...)` -metoden:

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

### <a name="create-consumer-user-accounts"></a>Skapa konsument användar konton
När du skapar användar konton i B2C-klienten kan du skicka en http- `POST` begäran `/users` till slut punkten:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

De flesta av dessa egenskaper i denna begäran krävs för att skapa konsument användare. Klicka [här](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)om du vill veta mer. Observera att `//` kommentarerna har inkluderats för illustration. Ta inte med dem i en faktisk begäran.

Kör något av följande kommandon för att se begäran:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Kommandot tar en JSON-fil som en indataparameter. Detta innehåller en JSON-representation av ett användar objekt. Det finns två exempel på. JSON-filer i exempel koden `usertemplate-email.json` : `usertemplate-username.json`och. Du kan ändra filerna så att de passar dina behov. Förutom de obligatoriska fälten ovan ingår flera valfria fält som du kan använda i de här filerna. Information om de valfria fälten finns i [enhets referens för Azure AD Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Du kan se hur POST-begäran konstrueras i `B2CGraphClient.SendGraphPostRequest(...)`.

* Den bifogar en åtkomsttoken till `Authorization` rubriken för begäran.
* Den anger `api-version=1.6`.
* Den inkluderar JSON-användarobjektet i bröd texten i begäran.

> [!NOTE]
> Om de konton som du vill migrera från ett befintligt användar Arkiv har lägre lösen ords styrka än den [starka lösen ords styrkan som tillämpas av Azure AD B2C](/previous-versions/azure/jj943764(v=azure.100)), kan du inaktivera kravet på starkt lösen ord med `DisableStrongPassword` hjälp av värdet i `passwordPolicies`egenskap. Du kan till exempel ändra begäran om att skapa användare enligt följande: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Uppdatera konsument användar konton
När du uppdaterar användar objekt liknar processen den som du använder för att skapa användar objekt. Men den här processen använder http `PATCH` -metoden:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Försök att uppdatera en användare genom att uppdatera dina JSON-filer med nya data. Sedan kan du använda `B2CGraphClient` för att köra ett av följande kommandon:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

`B2CGraphClient.SendGraphPatchRequest(...)` Granska metoden för information om hur du skickar den här begäran.

### <a name="search-users"></a>Sök efter användare
Du kan söka efter användare i B2C-klienten på ett par olika sätt. En, med hjälp av användarens objekt-ID eller två, med hjälp av användarens inloggnings-identifierare (t `signInNames` . ex. egenskapen).

Kör något av följande kommandon för att söka efter en speciell användare:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Här följer några exempel:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Ta bort användare
Processen för att ta bort en användare är enkel. Använd http- `DELETE` metoden och skapa URL: en med rätt objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Om du vill se ett exempel, anger du det här kommandot och visar borttagnings förfrågan som skrivs ut till-konsolen:

```cmd
B2C Delete-User <object-id-of-user>
```

`B2CGraphClient.SendGraphDeleteRequest(...)` Granska metoden för information om hur du skickar den här begäran.

Du kan utföra många andra åtgärder med Azure AD Graph API utöver användar hantering. [Azure AD Graph API-referensen](/previous-versions/azure/ad/graph/api/api-catalog) innehåller information om varje åtgärd, tillsammans med exempel begär Anden.

## <a name="use-custom-attributes"></a>Använd anpassade attribut
De flesta konsument program behöver lagra viss typ av anpassad användar profil information. Ett sätt som du kan göra är att definiera ett anpassat attribut i B2C-klienten. Du kan sedan behandla attributet på samma sätt som du hanterar andra egenskaper för ett användar objekt. Du kan uppdatera attributet, ta bort attributet, fråga efter attributet, skicka attributet som ett anspråk i inloggnings-token med mera.

Information om hur du definierar ett anpassat attribut i B2C-klienten finns i [B2C-referens för anpassade attribut](active-directory-b2c-reference-custom-attr.md).

Du kan visa de anpassade attribut som definierats i din B2C- `B2CGraphClient`klient genom att använda:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Resultatet av dessa funktioner visar information om varje anpassat attribut, till exempel:

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

Du kan använda det fullständiga namnet, till exempel `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`som en egenskap för dina användar objekt.  Uppdatera. JSON-filen med den nya egenskapen och ett värde för egenskapen och kör sedan:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Genom att `B2CGraphClient`använda har du ett tjänst program som kan hantera B2C-klientens användare program mässigt. `B2CGraphClient`använder sin egen program identitet för att autentisera till Azure AD-Graph API. Den hämtar också token med hjälp av en klient hemlighet. Kom ihåg några viktiga punkter för B2C-appar när du införlivar den här funktionen i ditt program:

* Du måste ge programmet rätt behörighet i klient organisationen.
* För tillfället måste du använda ADAL (inte MSAL) för att hämta åtkomsttoken. (Du kan också skicka protokoll meddelanden direkt, utan att använda ett bibliotek.)
* När du anropar Graph API använder `api-version=1.6`du.
* När du skapar och uppdaterar konsument användare krävs några egenskaper, enligt beskrivningen ovan.

Om du har frågor eller förfrågningar om åtgärder som du vill utföra med hjälp av Graph API på B2C-klienten, lämnar du en kommentar om den här artikeln eller filen ett problem i exempel databasen för GitHub-kod.

