---
title: Använda Graph API i Azure Active Directory B2C | Microsoft Docs
description: Så här att anropa Graph API för en B2C-klient med hjälp av en programidentitet för att automatisera processen.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ce4446f52fec4312466fc18cb97e25e93358ee1a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697931"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Använd Azure AD Graph API:n

>[!NOTE]
> Du måste använda den [Azure AD Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) att hantera användare i en Azure AD B2C-katalog. Detta skiljer sig från Microsoft Graph API. Lär dig mer [här](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Azure Active Directory (Azure AD) B2C-klienter brukar vara mycket stor. Det innebär att många vanliga klient administrationsuppgifter måste utföras via programmering. En primärt exempel är användarhantering. Du kan behöva migrera en befintlig användararkivet till en B2C-klient. Du kanske vill vara värd för användarregistrering på sidan och skapa användarkonton i Azure AD B2C-katalogen i bakgrunden. Dessa typer av uppgifter kräver möjligheten att skapa, läsa, uppdatera och ta bort användarkonton. Du kan utföra dessa uppgifter med hjälp av Azure AD Graph API.

Det finns två primära lägen för att kommunicera med Graph API för B2C-klienter.

* Interaktiv, körs en gång aktivitetsinformation, bör du fungerar som ett administratörskonto i B2C-klienten när du utför uppgifterna. Det här läget kräver en administratör logga in med autentiseringsuppgifterna innan den administratören kan utföra alla anrop för Graph API.
* Automatiserad, kontinuerlig aktivitetsinformation, bör du använda någon typ av tjänstkonto som du anger med tillräckliga privilegier att utföra hanteringsuppgifter. I Azure AD kan du göra detta genom att registrera ett program och autentiserar till Azure AD. Detta görs med hjälp av en **program-ID** som använder den [beviljande av autentiseringsuppgifter för OAuth 2.0-klient](../active-directory/develop/service-to-service.md). I det här fallet fungerar programmet som själva inte som en användare att anropa Graph API.

I den här artikeln får du lära dig hur du utför automatiserade användningsfall. Du kommer att skapa en .NET 4.5 `B2CGraphClient` som utför användare skapa, läsa, uppdatera och ta bort CRUD-åtgärder. Klienten har ett Windows-kommandoradsgränssnitt (CLI) som gör att du kan anropa olika metoder. Men är koden skriven ska bete sig i ett icke-interaktiv, automatiserat sätt.

## <a name="get-an-azure-ad-b2c-tenant"></a>Skaffa en Azure AD B2C-klient
Innan du kan skapa program eller användare, behöver du en Azure AD B2C-klient. Om du inte redan har en klient [Kom igång med Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrera ditt program i din klient
När du har en B2C-klient kan du behöva registrera ditt program med hjälp av den [Azure-portalen](https://portal.azure.com).

> [!IMPORTANT]
> Om du vill använda Graph API med din B2C-klient som du behöver registrera ett program med hjälp av den *Appregistreringar* tjänsten på Azure-portalen **inte** Azure AD B2C *program*menyn. Följande instruktioner för att leda till lämplig menyn. Du kan inte återanvända befintliga B2C-program som du registrerade i Azure AD B2C *program* menyn.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj din Azure AD B2C-klient genom att välja kontot i det övre högra hörnet på sidan.
3. I det vänstra navigeringsfönstret väljer **alla tjänster**, klickar du på **Appregistreringar**, och klicka på **Lägg till**.
4. Följ anvisningarna och skapa ett nytt program. 
    1. Välj **Webbapp / API** som programtyp.    
    2. Ange **alla inloggnings-URL: er** (t.ex. `https://B2CGraphAPI`) eftersom den inte är relevanta för det här exemplet.  
5. I programmet kommer visas nu i listan med program, klicka på den för att hämta den **program-ID** (även kallat klient-ID). Kopiera den som du behöver det i ett senare avsnitt.
6. I menyn inställningar klickar du på **nycklar**.
7. I den **lösenord** avsnittet, ange nyckelbeskrivningen och välj en varaktighet och klicka sedan på **spara**. Kopiera nyckelvärdet (även kallat Klienthemlighet) för användning i ett senare avsnitt.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurera skapa, läsa och uppdatera behörigheter för ditt program
Nu måste du konfigurera ditt program att hämta alla behörigheter som krävs för att skapa, läsa, uppdatera och ta bort användare.

1. Du fortsätter i Azure portal Appregistreringar menyn, Välj ditt program.
2. I menyn inställningar klickar du på **behörigheter som krävs för**.
3. I menyn behörigheter som krävs för klickar du på **Windows Azure Active Directory**.
4. Aktivera åtkomst-menyn väljer du den **läsa och skriva katalogdata** behörighet från **programbehörigheter** och klicka på **spara**.
5. Klicka slutligen i menyn behörigheter som krävs på den **bevilja behörigheter** knappen.

Nu har du ett program som har behörighet att skapa, läsa och uppdatera användare från din B2C-klient.

> [!NOTE]
> Bevilja behörigheter kan ta några minuter att bearbeta.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Konfigurera ta bort eller uppdatera lösenordet behörigheter för ditt program
För närvarande den *läsa och skriva katalogdata* behörighet har **inte** är möjligheten att ta bort användare eller uppdatera användarlösenord. Om du vill ge ditt program möjlighet att ta bort användare eller update lösenord måste du utföra de här extra stegen som rör PowerShell, annars kan du gå till nästa avsnitt.

Om du inte redan har installerat, installerar du först den [Azure AD PowerShell v1-modulen (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

När du har installerat PowerShell-modulen att ansluta till din Azure AD B2C-klient.

> [!IMPORTANT]
> Du måste använda ett administratörskonto för B2C-klient som är **lokala** till B2C-klient. Dessa konton se ut så här: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Nu använder vi den **program-ID** i skriptet nedan för att tilldela programmet till administratörsrollen-konto. Dessa roller har välkänd identifierare, så du behöver matas in din **program-ID** i skriptet nedan.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Ditt program nu har även behörighet att ta bort användare eller uppdatera lösenord från din B2C-klient.

## <a name="download-configure-and-build-the-sample-code"></a>Hämta, konfigurera och skapa exempelkoden
Först hämta exempelkoden och få den körs. Sedan tar vi en närmare titt på den.  Du kan [har laddat ned exempelkoden som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Du kan också klona det i en katalog som du föredrar:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Öppna den `B2CGraphClient\B2CGraphClient.sln` Visual Studio-lösning i Visual Studio. I den `B2CGraphClient` projekt, öppna filen `App.config`. Ersätt tre appinställningarna med dina egna värden:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Högerklicka sedan på den `B2CGraphClient` lösningen och återskapa exemplet. Om du är klar kan du bör nu ha en `B2C.exe` körbar fil som finns i `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Skapa användare CRUD-åtgärder med hjälp av Graph API
Om du vill använda B2CGraphClient, öppna en `cmd` Windows kommandot fråga och ändra katalogen till den `Debug` directory. Kör sedan den `B2C Help` kommando.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

En kort beskrivning av varje kommando visas. Varje gång du anropar någon av dessa kommandon `B2CGraphClient` gör en begäran till Azure AD Graph API.

### <a name="get-an-access-token"></a>Hämta en åtkomsttoken
Varje begäran för Graph API kräver en åtkomsttoken för autentisering. `B2CGraphClient` använder den öppna källkoden Active Directory Authentication Library (ADAL) för att hämta åtkomsttoken. ADAL gör tokenförvärv enklare genom att tillhandahålla ett enkelt API och att ta hand om viktig information, till exempel cachelagring av åtkomsttoken. Du har inte använder ADAL för att hämta token, men. Du kan också hämta token genom att utforma HTTP-begäranden.

> [!NOTE]
> Det här kodexemplet använder ADAL v2 för att kunna kommunicera med Graph API.  Du måste använda ADAL v2 eller v3 för att få åtkomsttokens som kan användas med Azure AD Graph API.
> 
> 

När `B2CGraphClient` körs, skapas en instans av den `B2CGraphClient` klass. Konstruktören för den här klassen ställer in en ADAL-autentisering ställningarna:

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

Vi använder den `B2C Get-User` kommandot som exempel. När `B2C Get-User` anropas utan någon ytterligare indata och CLI-anrop i `B2CGraphClient.GetAllUsers(...)` metod. Den här metoden anropar `B2CGraphClient.SendGraphGetRequest(...)`, som skickar en HTTP GET-begäran för Graph API. Innan du `B2CGraphClient.SendGraphGetRequest(...)` skickar GET-begäran, först hämtar en token med hjälp av ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Du kan hämta en åtkomsttoken för Graph API genom att anropa ADAL `AuthenticationContext.AcquireToken(...)` metod. ADAL returnerar sedan ett `access_token` som representerar programmets identitet.

### <a name="read-users"></a>Läs användare
När du vill hämta en lista med användare eller hämta en viss användare från Graph API kan du skicka en HTTP `GET` begäran till den `/users` slutpunkt. En begäran om alla användare i en klient som ser ut så här:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Om du vill se den här begäran, kör du:

 ```cmd
 B2C Get-User
 ```

Det finns två viktiga saker att tänka på:

* Åtkomsttoken som anskaffats via ADAL har lagts till i den `Authorization` rubrik med hjälp av den `Bearer` schema.
* För B2C-klienter, måste du använda Frågeparametern `api-version=1.6`.

Båda dessa uppgifter ska hanteras i den `B2CGraphClient.SendGraphGetRequest(...)` metoden:

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

### <a name="create-consumer-user-accounts"></a>Skapa användarkonton för konsument
När du skapar användarkonton i din B2C-klient kan du skicka en HTTP `POST` begäran till den `/users` slutpunkt:

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

De flesta av de här egenskaperna i den här begäran krävs för att skapa konsumentanvändare. Mer information klickar du på [här](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Observera att den `//` kommentarer har inkluderats för bilden. Ta inte med dem i en faktiska begäran.

Om du vill se begäran, kör du något av följande kommandon:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Den `Create-User` kommandot tar en JSON-fil som indataparameter. Innehåller en JSON-representation av ett användarobjekt. Det finns två exempel .json-filer i exempelkoden: `usertemplate-email.json` och `usertemplate-username.json`. Du kan ändra de här filerna så att den passar dina behov. Förutom de obligatoriska fälten ovan ingår flera valfria fält som du kan använda i de här filerna. Information om de valfria fälten finns i den [Azure AD Graph API-referens för entiteten](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Du kan se hur POST-begäran är konstruerat av `B2CGraphClient.SendGraphPostRequest(...)`.

* Det bifogar en åtkomst-token för den `Authorization` huvudet i begäran.
* Anger `api-version=1.6`.
* Den innehåller JSON-användarobjektet i brödtexten i begäran.

> [!NOTE]
> Om de konton som du vill migrera från en befintlig användararkivet har lägre lösenordssäkerhet än den [stark lösenordssäkerhet som tillämpas av Azure AD B2C](/previous-versions/azure/jj943764(v=azure.100)), kan du inaktivera den starkt lösenord krav med hjälp av den `DisableStrongPassword` värde i den `passwordPolicies` egenskapen. Exempelvis kan du ändra skapa användarbegäran som anges ovan på följande sätt: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Uppdatera konsument användarkonton
När du uppdaterar användarobjekt liknar processen det som du använder för att skapa användarobjekt. Men den här processen använder HTTP `PATCH` metoden:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Försök att uppdatera en användare genom att uppdatera JSON-filer med nya data. Du kan sedan använda `B2CGraphClient` att köra något av följande kommandon:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Granska den `B2CGraphClient.SendGraphPatchRequest(...)` metod för information om hur du skickar denna begäran.

### <a name="search-users"></a>Söka efter användare
Du kan söka efter användare i din B2C-klient på ett par olika sätt. En med hjälp av användarens objekt-ID eller två, med hjälp av användarens inloggning identifierare (d.v.s. den `signInNames` egenskapen).

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
Processen för att ta bort en användare är enkelt. Använd HTTP `DELETE` metoden och konstruktion URL med rätt objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Anger det här kommandot om du vill se ett exempel och visa delete-begäran som skrivs ut till konsolen:

```cmd
B2C Delete-User <object-id-of-user>
```

Granska den `B2CGraphClient.SendGraphDeleteRequest(...)` metod för information om hur du skickar denna begäran.

Du kan utföra många andra åtgärder med Azure AD Graph-API förutom hantering av användare. Den [Azure AD Graph API-referens](/previous-versions/azure/ad/graph/api/api-catalog) innehåller information om varje åtgärd, tillsammans med exempelförfrågan.

## <a name="use-custom-attributes"></a>Använd anpassade attribut
De flesta konsumentprogram behöva lagra någon typ av anpassad information om användarprofiler. Ett sätt som du kan göra detta är att definiera ett anpassat attribut i din B2C-klient. Du kan sedan hantera attributet på samma sätt som du behandla någon annan egenskap på ett användarobjekt. Du kan uppdatera attributet, ta bort attributet, fråga med attributet, skicka attributet som ett anspråk i token-inloggningen och mycket annat.

För att definiera ett anpassat attribut i din B2C-klient, finns det [B2C anpassade attributreferensen](active-directory-b2c-reference-custom-attr.md).

Du kan visa anpassade attribut som definierats i din B2C-klient med hjälp av `B2CGraphClient`:

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

Du kan använda det fullständiga namnet som `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, som en egenskap för ditt objekt.  Uppdatera din JSON-fil med ny egenskap och ett värde för egenskapen och kör sedan:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Med hjälp av `B2CGraphClient`, du har ett tjänstprogram som kan hantera din B2C-klientanvändare programmässigt. `B2CGraphClient` använder sin egen applikationsidentitet för att autentisera till Azure AD Graph API. Den hämtar även token med hjälp av en klienthemlighet. Kom ihåg några viktiga punkter för B2C-appar som du använda den här funktionaliteten i ditt program:

* Du måste bevilja programmet behörighet i klienten.
* Du måste använda ADAL (inte MSAL) för att få åtkomst-token för tillfället. (Du kan också skicka protokollmeddelanden direkt, utan att använda ett bibliotek.)
* När du anropar Graph API använder `api-version=1.6`.
* När du skapar och uppdaterar konsumentanvändare, krävs några egenskaper, enligt beskrivningen ovan.

Om du har några frågor eller begäranden för åtgärder som du vill utföra med hjälp av Graph API på din B2C-klient kan lämna en kommentar på den här artikeln eller om du rapportera problemet i GitHub-exempellagringsplatsen kod.

