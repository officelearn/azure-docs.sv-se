---
title: Använd Graph API - Azure AD B2C | Microsoft Docs
description: Så att anropa Graph API för en B2C-klient med hjälp av en programidentitet för att automatisera processen.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.openlocfilehash: 731ff24fe9cc1b5dbf0c597139a96ae80b863cc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Använda Azure AD Graph API

>[!NOTE]
> Du måste använda den [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) att hantera användare i en Azure AD B2C-katalog. Detta skiljer sig från Microsoft Graph API. Lär dig mer [här](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Azure Active Directory (AD Azure) B2C hyresgäster tenderar att vara mycket stora. Det innebär att många vanliga klient administrativa uppgifter måste utföras via programmering. En primär exempel är användarhantering. Du kan behöva migrera en befintlig användararkivet till en B2C-klient. Du kanske vill vara värd för användarregistrering på sidan och skapa användarkonton i Azure AD B2C-katalogen i bakgrunden. Dessa typer av uppgifter kräver möjlighet att skapa, läsa, uppdatera och ta bort användarkonton. Du kan utföra dessa uppgifter med hjälp av Azure AD Graph API.

För B2C-innehavare finns det två primära lägen kommunicera med Graph API.

* För interaktiv, körs en gång aktiviteter, bör du fungerar som ett administratörskontot i B2C-klient när du utför uppgifterna. Det här läget kräver att en administratör att logga in med autentiseringsuppgifterna som administratören ska kunna utföra alla anrop för Graph API.
* Du bör använda någon typ av tjänstkontot som att du förser med den behörighet som krävs för att utföra hanteringsuppgifter för automatisk, kontinuerlig uppgifter. Du kan göra detta genom att registrera ett program och autentisera till Azure AD i Azure AD. Detta görs med hjälp av en **program-ID** som använder den [OAuth 2.0 klientens autentiseringsuppgifter bevilja](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). I det här fallet fungerar programmet som själva inte som en användare att anropa Graph API.

I den här artikeln får du lära dig hur du utför automatiserade användningsfall. Du skapar ett .NET 4.5 `B2CGraphClient` som utför användare skapa, läsa, uppdatera och ta bort CRUD-åtgärder. Klienten har en Windows-kommandoradsgränssnittet (CLI) som gör att du kan anropa olika metoder. Men är koden skriven ska bete sig i ett icke-interaktiv, automatiserat sätt.

## <a name="get-an-azure-ad-b2c-tenant"></a>Skaffa en Azure AD B2C-klient
Innan du kan skapa program eller användare, behöver du en Azure AD B2C-klient. Om du inte redan har en klient [Kom igång med Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrera ditt program i din klientorganisation
När du har en B2C-klient, måste du registrera programmet med hjälp av den [Azure-portalen](https://portal.azure.com).

> [!IMPORTANT]
> Om du vill använda Graph-API med din B2C-klient, måste du registrera ett program som använder den *App registreringar* tjänsten i Azure-portalen **inte** Azure AD B2C *program*menyn. Följande instruktioner leder dig till rätt-menyn. Du kan inte återanvända befintliga B2C-program som du har registrerat i Azure AD B2C *program* menyn.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj din Azure AD B2C-klient genom att välja kontot i det övre högra hörnet på sidan.
3. I det vänstra navigeringsfönstret väljer **alla tjänster**, klickar du på **App registreringar**, och klicka på **Lägg till**.
4. Följ anvisningarna och skapa ett nytt program. 
    1. Välj **Webbapp / API** typen av program.    
    2. Ange **alla inloggnings-URL** (t.ex. https://B2CGraphAPI) eftersom den inte är relevanta för det här exemplet.  
5. Det program kommer nu visas i listan med program, klickar du på den för att hämta den **program-ID** (även kallat klient-ID). Kopiera den som du behöver i ett senare avsnitt.
6. Klicka på menyn inställningar **nycklar**.
7. I den **lösenord** avsnittet Ange viktiga beskrivning och välj en varaktighet, och klicka sedan på **spara**. Kopiera värdet för nyckeln (även kallat Klienthemlighet) för användning i ett senare avsnitt.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurera skapa, läsa och uppdatera behörigheterna för ditt program
Nu måste du konfigurera ditt program att hämta alla behörigheter som krävs för att skapa, läsa, uppdatera och ta bort användare.

1. Fortsätter den Azure-portalen App registreringar-menyn, Välj ditt program.
2. I menyn inställningar klickar du på **nödvändiga behörigheter**.
3. I menyn behörighet klickar du på **Windows Azure Active Directory**.
4. Aktivera åtkomst-menyn väljer du den **läsning och skrivning katalogdata** tillstånd från **programbehörigheter** och klicka på **spara**.
5. Slutligen tillbaka i behörighet-menyn klickar du på den **bevilja med** knappen.

Nu har du ett program som har behörighet att skapa, läsa och uppdatera användare från din B2C-klient.

> [!NOTE]
> Beviljar behörigheter att ta en stund att bearbeta klart.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Konfigurera behörighet att ta bort programmet
För närvarande den *läsning och skrivning katalogdata* behörighet har **inte** omfattar möjligheten att göra eventuella borttagningar, till exempel ta bort användare. Om du vill ge ditt program möjlighet att ta bort användare måste du utföra de här extra stegen som rör PowerShell, annars kan du gå till nästa avsnitt.

Om du inte redan har installerats, installerar du först den [Azure AD PowerShell v1-modulen (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

När du har installerat PowerShell-modulen att ansluta till din Azure AD B2C-klient.

> [!IMPORTANT]
> Du måste använda ett administratörskonto för B2C-klient som är **lokala** till B2C-klient. Dessa konton se ut så här: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Nu ska vi använda den **program-ID** i skriptet nedan för att tilldela användarens konto rollen administratör så att den att ta bort användare för programmet. Dessa roller har välkända identifierare, så du behöver matas in din **program-ID** i skriptet nedan.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Ditt program nu har även behörighet att ta bort användare från din B2C-klient.

## <a name="download-configure-and-build-the-sample-code"></a>Hämta, konfigurera och skapa exempelkoden
Först hämta exempelkoden och få det körs. Sedan tar vi en närmare titt på den.  Du kan [hämta exempelkoden som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Du kan också klona den i en katalog som du väljer:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Öppna den `B2CGraphClient\B2CGraphClient.sln` Visual Studio-lösning i Visual Studio. I den `B2CGraphClient` projekt, öppna filen `App.config`. Ersätt tre appinställningarna med egna värden:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Högerklicka sedan på den `B2CGraphClient` lösningen och återskapa exemplet. Om du lyckas kan du bör nu ha en `B2C.exe` körbar fil som finns i `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Skapa användaren CRUD-åtgärder med hjälp av Graph-API
Om du vill använda B2CGraphClient, öppna en `cmd` Windows kommandot fråga och ändra katalogen till den `Debug` directory. Kör sedan den `B2C Help` kommando.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Då visas en kort beskrivning av varje kommando. Varje gång du anropar någon av dessa kommandon `B2CGraphClient` gör en begäran till Azure AD Graph API.

### <a name="get-an-access-token"></a>Hämta en åtkomsttoken
Alla förfrågningar för Graph API kräver en åtkomst-token för autentisering. `B2CGraphClient` använder den öppen källkod Active Directory Authentication Library (ADAL) för att få åtkomst-token. ADAL underlättar token förvärv genom att tillhandahålla ett enkelt API och tar hand om vissa viktig information, till exempel cachelagring åtkomsttoken. Du inte använder ADAL för att hämta token, men. Du kan också hämta token genom att utforma HTTP-begäranden.

> [!NOTE]
> Det här kodexemplet använder ADAL v2 för att kunna kommunicera med Graph API.  Du måste använda ADAL version 2 eller 3 för att få åtkomst-token som kan användas med Azure AD Graph API.
> 
> 

När `B2CGraphClient` körs, skapas en instans av den `B2CGraphClient` klass. Konstruktören för den här klassen ställer in en scaffold-teknik för ADAL-autentisering:

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

Vi använder den `B2C Get-User` kommandot som exempel. När `B2C Get-User` anropas utan några ytterligare indata CLI-anrop i `B2CGraphClient.GetAllUsers(...)` metod. Den här metoden anropar `B2CGraphClient.SendGraphGetRequest(...)`, som skickar en HTTP GET-begäran för Graph API. Innan `B2CGraphClient.SendGraphGetRequest(...)` skickar GET-begäran, först hämtar en åtkomst-token med hjälp av ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Du kan hämta ett åtkomsttoken för Graph API genom att anropa ADAL `AuthenticationContext.AcquireToken(...)` metod. ADAL returnerar en `access_token` som representerar programmets identitet.

### <a name="read-users"></a>Läs användare
När du vill hämta en lista med användare eller hämta en viss användare från Graph API kan du skicka en HTTP `GET` begäran om att den `/users` slutpunkt. En begäran för alla användare i en klient som ser ut så här:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Om du vill se den här begäran, kör du:

 ```cmd
 B2C Get-User
 ```

Det finns två viktiga saker att tänka på:

* Åtkomsttoken har köpt via ADAL har lagts till i den `Authorization` sidhuvud med hjälp av den `Bearer` schema.
* För B2C-innehavare, måste du använda Frågeparametern `api-version=1.6`.

Båda dessa uppgifter hanteras i den `B2CGraphClient.SendGraphGetRequest(...)` metoden:

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

### <a name="create-consumer-user-accounts"></a>Skapa användarkonton för konsumenten
När du skapar användarkonton i din B2C-klient kan du skicka en HTTP `POST` begäran om att den `/users` slutpunkt:

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

De flesta av de här egenskaperna i den här begäran krävs för att skapa konsumentanvändare. Mer information klickar du på [här](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Observera att den `//` kommentarer har inkluderats för bilden. Ta inte med dem i en faktiska begäran.

Om du vill se begäran, kör du något av följande kommandon:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Den `Create-User` kommandot tar emot en JSON-fil som indataparameter. Innehåller en JSON-representation av ett användarobjekt. Det finns två exempel JSON-filer i exempelkoden: `usertemplate-email.json` och `usertemplate-username.json`. Du kan ändra de här filerna så att de passar dina behov. Förutom de obligatoriska fälten ovan ingår flera valfria fälten som du kan använda i dessa filer. Information om de valfria fälten kan hittas i den [Azure AD Graph API Entitetsreferens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Du kan se hur POST-begäran har skapats i `B2CGraphClient.SendGraphPostRequest(...)`.

* En åtkomsttoken att bifogas i `Authorization` huvudet i begäran.
* Anger `api-version=1.6`.
* Brödtexten i begäran innehåller JSON användarobjektet.

> [!NOTE]
> Om de konton som du vill migrera från en befintlig användararkivet har lägre lösenordssäkerhet än den [starka lösenord tillämpas av Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), kan du inaktivera krav på starkt lösenord med den `DisableStrongPassword` värde i den `passwordPolicies` egenskapen. Exempelvis kan du ändra användarens begäran om att skapa som angetts ovan på följande sätt: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Uppdatera konsumenten användarkonton
När du uppdaterar användarobjekt liknar processen det du använder för att skapa användarobjekt. Men den här processen använder HTTP `PATCH` metoden:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Försök att uppdatera en användare genom att uppdatera JSON-filer med nya data. Du kan sedan använda `B2CGraphClient` att köra något av följande kommandon:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Kontrollera den `B2CGraphClient.SendGraphPatchRequest(...)` metod för information om hur du skickar denna begäran.

### <a name="search-users"></a>Söka efter användare
Du kan söka efter användare i din B2C-klient på ett par olika sätt. En med hjälp av användarens objekt-ID eller två, med hjälp av användarens inloggning Identiferare (d.v.s. de `signInNames` egenskap).

Kör något av följande kommandon för att söka efter en viss användare:

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
Processen för att ta bort en användare är enkelt. Använda HTTP `DELETE` metoden och konstruktion URL med rätt objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Anger det här kommandot och visa begäran om borttagning som skrivs ut på konsolen om du vill se ett exempel:

```cmd
B2C Delete-User <object-id-of-user>
```

Kontrollera den `B2CGraphClient.SendGraphDeleteRequest(...)` metod för information om hur du skickar denna begäran.

Du kan utföra många andra åtgärder med Azure AD Graph API förutom användarhantering. Den [Azure AD Graph API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) innehåller information om varje åtgärd, tillsammans med exempel begäranden.

## <a name="use-custom-attributes"></a>Använd anpassade attribut
De flesta konsumenten program behöver lagra någon typ av anpassad profil användarinformation. Ett sätt som du kan göra detta är att definiera ett anpassat attribut i din B2C-klient. Du kan sedan hantera attributet på samma sätt som du hanterar andra egenskapen på ett användarobjekt. Du kan uppdatera attributet, ta bort attributet, fråga med attributet, skicka attributet som ett anspråk i inloggning-token och mycket mer.

Om du vill definiera ett anpassat attribut i din B2C-klient, finns det [referens för B2C-attributet](active-directory-b2c-reference-custom-attr.md).

Du kan visa de anpassade attribut som definierats i din B2C-klient med hjälp av `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Resultatet av dessa funktioner visar information om varje anpassade attribut som:

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

Du kan använda det fullständiga namnet som `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, som en egenskap på din användarobjekt.  Uppdatera JSON-fil med den nya egenskapen och ett värde för egenskapen och kör sedan:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Med hjälp av `B2CGraphClient`, du har ett tjänstprogram som kan hantera B2C-klient användarna programmässigt. `B2CGraphClient` använder en egen programidentitet för att autentisera till Azure AD Graph API. Token får också med hjälp av en klienthemlighet. Kom ihåg några viktiga punkter för B2C-appar som du använda den här funktionaliteten i ditt program:

* Du måste ge rätt behörighet i klienten för programmet.
* Du måste använda ADAL (inte MSAL) för att få åtkomst-token för tillfället. (Du kan också skicka protokollmeddelanden direkt, utan att använda ett bibliotek.)
* När du anropar Graph API, Använd `api-version=1.6`.
* När du skapar och uppdaterar konsumentanvändare krävs några egenskaper, enligt beskrivningen ovan.

Om du har några frågor eller begäran om åtgärder som du vill utföra med hjälp av Graph API på din B2C-klient, lämna en kommentar på den här artikeln eller filen ett problem i GitHub-lagringsplatsen kod exempel.

