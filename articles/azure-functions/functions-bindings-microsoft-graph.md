---
title: Azure Functions Microsoft Graph bindningar | Microsoft Docs
description: "Förstå hur du använder Microsoft Graph utlösare och bindningar i Azure Functions."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: dd7bcd57260b9763eabb9b4c915d9ff46e79e931
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Azure Functions Microsoft Graph-bindningar

Den här artikeln beskriver hur du konfigurerar och arbetar med Microsoft Graph utlösare och bindningar i Azure Functions.
Med dessa kan du använda Azure Functions för att arbeta med data, insikter och händelser från den [Microsoft Graph](https://graph.microsoft.io).

Microsoft Graph filnamnstillägget innehåller följande Bindningar:
- En [auth token inkommande bindningen](#token-input) kan du samverka med eventuella Microsoft Graph API.
- En [Excel-tabellen inkommande bindningen](#excel-input) kan du läsa data från Excel.
- En [Excel-tabellen utdatabindning](#excel-output) kan du ändra Excel-data.
- En [OneDrive filen inkommande bindningen](#onedrive-input) kan du läsa filer från OneDrive.
- En [OneDrive filen utdatabindning](#onedrive-output) gör att du kan skriva till filer i OneDrive.
- En [Outlook-meddelande utdatabindning](#outlook-output) kan du skicka e-post via Outlook.
- En samling [Microsoft Graph webhook utlösare och bindningar](#webhooks) kan du ta hänsyn till händelser från Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Microsoft Graph-bindningar finns för närvarande under förhandsgranskning.

## <a name="setting-up-the-extensions"></a>Konfigurera tillägg

Microsoft Graph bindningar är tillgängliga via _bindning tillägg_. Bindningen tillägg är valfria komponenter till Azure Functions-runtime. Det här avsnittet visas hur du ställer in av Microsoft Graph och auth token tillägg.

### <a name="enabling-functions-20-preview"></a>Aktivera funktioner 2.0 preview

Bindningen tillägg är bara tillgängliga endast för förhandsversionen av Azure Functions 2.0. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Läs mer i [så avsedda för Azure Functions-runtime versioner](functions-versions.md).

### <a name="installing-the-extension"></a>Installationen av tillägget

Om du vill installera ett tillägg från Azure-portalen måste du gå till en mall eller bindning som hänvisar till. Skapa en ny funktion och välj ”Microsoft Graph”-scenario i urvalssidan mallen. Välj en av mallar från det här scenariot. Du kan också gå till fliken ”integrera” i en befintlig funktion och välja en av de bindningar som beskrivs i det här avsnittet.

I båda fallen visas en varning som anger att tillägget ska kunna installeras. Klicka på **installera** att hämta tillägget.

> [!Note] 
> Varje tillägg behöver bara installeras en gång per funktionsapp. I portalen-installationen kan ta upp till 10 minuter på en plan för användning.

Om du använder Visual Studio kan du hämta tillägg genom att installera dessa NuGet-paket:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Konfigurera App Service autentisering / auktorisering

De bindningar som beskrivs i det här avsnittet kräver en identitet som ska användas. På så sätt kan Microsoft Graph att tillämpa behörigheter och granska interaktioner. Identiteten kan vara en användare att komma åt programmet eller programmet. Om du vill konfigurera den här identiteten, behöver du ställa in [App Service autentisering / auktorisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) med Azure Active Directory. Du måste också begära alla dina funktioner kräver resursbehörigheter.

> [!Note] 
> Microsoft Graph filnamnstillägget har endast stöd för AAD-autentisering. Användarna måste logga in med ett arbets- eller skolkonto.

Om du använder Azure-portalen nedan tillfrågas om du vill installera tillägg, visas en varning som uppmanar dig att konfigurera App Service-autentisering / auktorisering och begära alla behörigheter mallen eller bindning krävs. Klicka på **nu konfigurera AAD** eller **lägga till behörigheter nu** efter behov.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Auth token indatabindning

Den här bindningen hämtar en AAD-token för en viss resurs och förser koden som en sträng. Resursen kan vara någon som har behörighet för programmet. 

### <a name="configuring-an-auth-token-input-binding"></a>Konfigurera en token indatabindning auth

Bindningen själva kräver inte några AAD-behörigheter, men beroende på hur token som används, kan du behöva begär ytterligare behörighet. Kontrollera kraven för den resurs som du vill komma åt med token.

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för auth-token. Se [med hjälp av en token för auth inkommande bindningen från kod](#token-input-code).|
|**typ**|Krävs – måste vara inställd på `token`.|
|**riktning**|Krävs – måste vara inställd på `in`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**resursen**|Krävs – en AAD resurs-URL som token begärs.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Med hjälp av en auth token indatabindning från kod

Token visas alltid till kod som en sträng.

#### <a name="sample-getting-user-profile-information"></a>Exempel: Hämta användarens profilinformation

Anta att du har följande function.json som definierar en HTTP-utlösare med en token indatabindning:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

I följande exempel JS använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet. I den `function.json` ovan, ändra `$return` till `res` första.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Excel-tabellen indatabindning

Den här bindningen läser innehållet i en Excel-tabell som lagras i OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Konfigurera en indatabindning för Excel-tabell

Den här bindningen kräver AAD följande behörigheter:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läs användarfiler|

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för Excel-tabellen. Se [med hjälp av en Excel-tabell inkommande bindningen från kod](#excel-input-code).|
|**typ**|Krävs – måste vara inställd på `excel`.|
|**riktning**|Krävs – måste vara inställd på `in`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**sökväg**|Obligatoriskt - sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|Kalkylbladet som hittas i tabellen.|
|**tableName**|Namnet på tabellen. Om inget anges används innehållet i kalkylbladet.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Med hjälp av en Excel tabell indatabindning från kod

Bindningen visar följande typer till .NET-funktioner:
- String []]
- Microsoft.Graph.WorkbookTable
- Anpassade objektgrupper (med strukturella modellbindning)

#### <a name="sample-reading-an-excel-table"></a>Exempel: Läsa en Excel-tabell

Anta att du har följande function.json som definierar en HTTP-utlösare med en Excel-indatabindning:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel lägger till läser innehållet i den angivna tabellen och returnerar dem till användaren:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

I följande exempel JS lägger till läser innehållet i den angivna tabellen och returnerar dem till användaren. I den `function.json` ovan, ändra `$return` till `res` första.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Excel-tabellen utdatabindning

Den här bindningen ändrar innehållet i en Excel-tabell som lagras i OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Konfigurera en Excel-tabell utdatabindning

Den här bindningen kräver AAD följande behörigheter:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för auth-token. Se [med hjälp av en Excel-tabell utdatabindning från kod](#excel-output-code).|
|**typ**|Krävs – måste vara inställd på `excel`.|
|**riktning**|Krävs – måste vara inställd på `out`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**sökväg**|Obligatoriskt - sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|Kalkylbladet som hittas i tabellen.|
|**tableName**|Namnet på tabellen. Om inget anges används innehållet i kalkylbladet.|
|**Uppdateringstyp**|Obligatoriskt - typ av ändring som du gör i tabellen. Kan vara något av följande värden:<ul><li><code>update</code>-Ersätter innehållet i tabellen i OneDrive.</li><li><code>append</code>-Lägger till nyttolasten i slutet av tabellen i OneDrive genom att skapa nya rader.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Med hjälp av en Excel-tabell utdatabindning från kod

Bindningen visar följande typer till .NET-funktioner:
- String []]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Anpassade objektgrupper (med strukturella modellbindning)

#### <a name="sample-adding-rows-to-an-excel-table"></a>Exempel: Lägga till rader i en Excel-tabell

Anta att du har följande function.json som definierar en HTTP-utlösare med en Excel-utdatabindning:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


Följande C#-exempel lägger till en ny rad i tabellen (antas vara en kolumn) baserat på indata från frågesträngen:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

I följande exempel JS lägger till en ny rad i tabellen (antas vara en kolumn) baserat på indata från frågesträngen. I den `function.json` ovan, ändra `$return` till `res` första.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>OneDrive filen indatabindning

Den här bindningen läser innehållet i en fil som lagras i OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Konfigurera en inkommande bindning för OneDrive-fil

Den här bindningen kräver AAD följande behörigheter:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läs användarfiler|

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i funktionskod för filen. Se [med hjälp av en fil på OneDrive inkommande bindningen från kod](#onedrive-input-code).|
|**typ**|Krävs – måste vara inställd på `onedrive`.|
|**riktning**|Krävs – måste vara inställd på `in`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**sökväg**|Krävs – sökvägen till filen i OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Med hjälp av en OneDrive fil indatabindning från kod

Bindningen visar följande typer till .NET-funktioner:
- byte]
- Stream
- Sträng
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Exempel: Läsa en fil från OneDrive

Anta att du har följande function.json som definierar en HTTP-utlösare med OneDrive-indatabindning:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel läser filen som anges i frågesträngen och loggar längden:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

I följande exempel JS läser filen som anges i frågesträngen och returnerar längden. I den `function.json` ovan, ändra `$return` till `res` första.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>OneDrive filen utdatabindning

Den här bindningen ändrar innehållet i en fil som lagras i OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Konfigurera en OneDrive utdatabindning filen

Den här bindningen kräver AAD följande behörigheter:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i funktionskod för filen. Se [med hjälp av en fil på OneDrive-utdatabindning från kod](#onedrive-output-code).|
|**typ**|Krävs – måste vara inställd på `onedrive`.|
|**riktning**|Krävs – måste vara inställd på `out`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**sökväg**|Krävs – sökvägen till filen i OneDrive.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Med hjälp av en OneDrive utdatabindning filen från kod

Bindningen visar följande typer till .NET-funktioner:
- byte]
- Stream
- Sträng
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Exempel: Skrivning till en fil på OneDrive

Anta att du har följande function.json som definierar en HTTP-utlösare med en OneDrive utdatabindning:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt enligt definitionen i konfigurationen ovan) i roten på anroparens OneDrive:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
I följande exempel JS hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt som definierats i konfigurationen ovan) i roten på anroparens OneDrive. I den `function.json` ovan, ändra `$return` till `res` första.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Outlook-meddelande utdatabindning

Skickar ett e-postmeddelande via Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Konfigurera ett Outlook-meddelande utdatabindning

Den här bindningen kräver AAD följande behörigheter:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Skicka e-post som användare|

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**|Krävs – måste vara inställd på `outlook`.|
|**riktning**|Krävs – måste vara inställd på `out`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Med hjälp av ett Outlook-meddelande utdatabindning från kod

Bindningen visar följande typer till .NET-funktioner:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- Sträng
- Anpassade objektgrupper (med strukturella modellbindning)

#### <a name="sample-sending-an-email-through-outlook"></a>Exempel: Skicka ett e-postmeddelande i Outlook

Anta att du har följande utdatabindning function.json som definierar en HTTP-utlösare med ett Outlook-meddelande:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel skickar ett e-postmeddelande från anroparen till en mottagare som angetts i frågesträngen.

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

I följande exempel JS skickar ett e-postmeddelande från anroparen till en mottagare som angetts i frågesträngen.

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Microsoft Graph webhook bindningar

Webhooks kan du ta hänsyn till händelser i Microsoft Graph. För att stödja webhooks, funktioner som krävs för att skapa, uppdatera och ta hänsyn till _webhook prenumerationer_. En fullständig webhook-lösning kräver en kombination av bindningarna som följande:
- En [Microsoft Graph webhook utlösaren](#webhook-trigger) kan du ta hänsyn till en inkommande webhook.
- En [Microsoft Graph webhook prenumeration inkommande bindningen](#webhook-input) kan du visa en lista med befintliga prenumerationer och du kan också uppdatera dem.
- En [Microsoft Graph webhook prenumeration utdatabindning](#webhook-output) gör att du kan skapa eller ta bort webhooken prenumerationer.

Bindningar själva kräver inte några AAD-behörigheter, men du kommer att behöva begära behörigheter som är relevanta för den resurstyp som du vill ta hänsyn till. En lista som behövs för varje resurstyp av finns [Prenumerationsbehörighet](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Mer information om webhooks finns [arbeta med webhooks i Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Microsoft Graph webhook utlösare

Den här utlösaren kan en funktion att reagera på en inkommande webhook från Microsoft Graph. Varje instans av den här utlösaren kan reagera på en resurstyp för Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Konfigurera en utlösare för Microsoft Graph-webhook

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**|Krävs – måste vara inställd på `graphWebhook`.|
|**riktning**|Krävs – måste vara inställd på `trigger`.|
|**resourceType**|Obligatoriskt - diagram-resurs som den här funktionen ska svara på webhooks. Kan vara något av följande värden:<ul><li><code>#Microsoft.Graph.Message</code>-ändringar som gjorts i Outlook-meddelanden.</li><li><code>#Microsoft.Graph.DriveItem</code>-ändrade OneDrive rot-objekt.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> En funktionsapp kan bara ha en funktion som har registrerats mot en given `resourceType` värde.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Med hjälp av en Microsoft Graph webhook utlösare från kod

Bindningen visar följande typer till .NET-funktioner:
- Microsoft Graph SDK-typer till resursen skriver, t.ex. Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Anpassade objektgrupper (med strukturella modellbindning)

Exempel på hur du använder den här bindningen i koden finns [Microsoft Graph webhook exempel](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Microsoft Graph webhook prenumeration indatabindning

Den här bindningen kan du hämta listan över prenumerationer som hanteras av den här funktionen appen. Bindningen läser från funktionen app lagring och återspeglar inte andra prenumerationer som skapats från utanför appen.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Konfigurera en inkommande bindning för Microsoft Graph webhook-prenumeration

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**|Krävs – måste vara inställd på `graphWebhookSubscription`.|
|**riktning**|Krävs – måste vara inställd på `in`.|
|**filter**| Om värdet `userFromRequest`, och sedan bindningen endast hämtar prenumerationer som ägs av den anropande användaren (bara giltigt med [HTTP-utlösaren]).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Med hjälp av en Microsoft Graph webhook prenumeration indatabindning från kod

Bindningen visar följande typer till .NET-funktioner:
- String]
- Anpassade objekt typen matriser
- Newtonsoft.Json.Linq.JObject]
- Microsoft.Graph.Subscription]

Exempel på hur du använder den här bindningen i koden finns [Microsoft Graph webhook exempel](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Webhook-prenumeration för Microsoft Graph-utdatabindning

Den här bindningen kan du skapa, ta bort och uppdatera webhook prenumerationer i Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Konfigurera en Microsoft Graph webhook utdatabindning prenumeration

Bindningen stöder följande egenskaper:

|Egenskap|Beskrivning|
|--------|--------|
|**Namn**|Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**|Krävs – måste vara inställd på `graphWebhookSubscription`.|
|**riktning**|Krävs – måste vara inställd på `out`.|
|**identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>– Endast giltigt med [HTTP-utlösaren]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code>-Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code>-Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code>-Använder identiteten för funktionen appen.</li></ul>|
|**användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**åtgärden**|Obligatoriskt – anger åtgärden bindningen ska utföra. Kan vara något av följande värden:<ul><li><code>create</code>-Registrerar en ny prenumeration.</li><li><code>delete</code>-Tar bort en angiven prenumeration.</li><li><code>refresh</code>– Uppdaterar en angiven prenumeration om du vill behålla upphör att gälla.</li></ul>|
|**subscriptionResource**|Krävs om och bara om den _åtgärd_ är inställd på `create`. Anger Microsoft Graph-resurs som ska övervakas för ändringar. Se [arbeta med webhooks i Microsoft Graph]. |
|**changeType**|Krävs om och bara om den _åtgärd_ är inställd på `create`. Anger vilken typ av ändring i den prenumererar på resurs som ska generera ett meddelande. Värdena som stöds är: `created`, `updated`, `deleted`. Du kan kombinera flera värden med hjälp av en kommaavgränsad lista.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Med hjälp av en Microsoft Graph webhook utdatabindning prenumeration från kod

Bindningen visar följande typer till .NET-funktioner:
- Sträng
- Microsoft.Graph.Subscription

Exempel på hur du använder den här bindningen i koden finns [Microsoft Graph webhook exempel](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Microsoft Graph webhook-exempel

#### <a name="sample-creating-a-subscription"></a>Exempel: Skapa en prenumeration

Anta att du har följande function.json som definierar en HTTP-utlösare med en prenumeration utdata bindning med åtgärden Skapa:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel registrerar en webhook som meddelar den här funktionen appen när den anropande användaren tar emot ett Outlook-meddelande:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

I följande exempel JS registrerar en webhook som meddelar den här funktionen appen när den anropande användaren tar emot ett Outlook-meddelande:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Exempel: Hantering av aviseringar

Anta att du har följande function.json som definierar diagram webhook utlösare för att hantera Outlook-meddelanden:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och som innehåller ”Azure Functions” i ämnesraden:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

I följande exempel JS reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och som innehåller ”Azure Functions” i ämnesraden:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>Exempel: Ta bort prenumerationer

Anta att du har följande function.json som definierar en HTTP-utlösare med en prenumeration indatabindning och en prenumeration utdata bindning med borttagningsåtgärden ta:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel hämtar alla prenumerationer för den anropande användaren och tar bort dem:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

I följande exempel JS hämtar alla prenumerationer för den anropande användaren och tar bort dem:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>Exempel: Uppdatera prenumerationer

Det finns två sätt att uppdatera prenumerationer:
- Använd programidentiteten för att hantera alla prenumerationer. Detta kräver godkännande från en Azure Active Directory-administratör. Detta kan användas av alla språk som stöds av Azure Functions.
- Använda det identitet som är associerade med varje prenumeration genom att manuellt binda varje användar-ID. Detta kräver vissa anpassad kod för att utföra bindningen. Detta kan endast användas av .NET-funktioner.

Båda alternativen visas nedan.

**Med hjälp av programidentiteten**

Anta att du har följande function.json som definierar en timer som utlösare med en prenumeration indatabindning prenumeration utdata bindning, med programidentiteten:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel uppdaterar prenumerationer på en timer som använder programidentiteten:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

I följande exempel JS uppdaterar prenumerationer på en timer som använder programidentiteten:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Med dynamisk användaridentiteter**

Anta att du har följande function.json som definierar en timer som utlösare, inkommande vänta bindning till prenumerationen bindningen till Funktionskoden för det andra alternativet:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Följande C#-exempel uppdaterar prenumerationer på en timer med varje användares identitet genom att skapa bindningen utdata i koden:
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[HTTP-utlösaren]: functions-bindings-http-webhook.md
[arbeta med webhooks i Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
