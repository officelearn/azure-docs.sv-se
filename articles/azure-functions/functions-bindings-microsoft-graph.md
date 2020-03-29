---
title: Microsoft Graph-bindningar för Azure-funktioner
description: Förstå hur du använder Microsoft Graph-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715029"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph-bindningar för Azure-funktioner

I den här artikeln beskrivs hur du konfigurerar och arbetar med Microsoft Graph-utlösare och bindningar i Azure Functions. Med dessa kan du använda Azure Functions för att arbeta med data, insikter och händelser från [Microsoft Graph](https://developer.microsoft.com/graph).

Microsoft Graph-tillägget innehåller följande bindningar:
- Med [en auth-tokenindatabindning](#token-input) kan du interagera med alla Microsoft Graph-API: er.
- Med en [Indatabindning i Excel](#excel-input) kan du läsa data från Excel.
- Med en [Excel-tabellutdatabindning](#excel-output) kan du ändra Excel-data.
- Med en [OneDrive-indatabindning](#onedrive-input) kan du läsa filer från OneDrive.
- Med en [OneDrive-filutdatabindning](#onedrive-output) kan du skriva till filer i OneDrive.
- Med [en Outlook-meddelandeutdatabindning](#outlook-output) kan du skicka e-post via Outlook.
- Med en samling [microsoft graph-webbkrokutlösare och bindningar](#webhooks) kan du reagera på händelser från Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph-bindningar är för närvarande i förhandsversion för Azure Functions version 2.x och senare. De stöds inte i Functions version 1.x.

## <a name="packages"></a>Paket

Auth token-indatabindningen finns i [paketet Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. De andra Microsoft Graph-bindningarna finns i [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph-paketet.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) Källkoden för paketen finns i GitHub-databasen för [azure-functions-microsoftgraph-extension.](https://github.com/Azure/azure-functions-microsoftgraph-extension/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Ställa in tilläggen

Microsoft Graph-bindningar är tillgängliga via _bindningstillägg_. Bindningstillägg är valfria komponenter till Azure Functions-körningen. I det här avsnittet visas hur du konfigurerar Microsoft Graph och auth-tokentillägg.

### <a name="enabling-functions-20-preview"></a>Aktivera förhandsgranskning av funktioner 2.0

Bindningstillägg är endast tillgängliga för förhandsversionen av Azure Functions 2.0. 

Information om hur du anger en funktionsapp för att använda förhandsversionen av körtiden Funktioner finns i [Så här inriktar du azure functions-körningsversioner](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installera tillägget

Om du vill installera ett tillägg från Azure-portalen navigerar du till antingen en mall eller bindning som refererar till den. Skapa en ny funktion, och medan i mallen urvalsskärmen, välj "Microsoft Graph" scenario. Välj en av mallarna i det här scenariot. Alternativt kan du navigera till fliken "Integrera" i en befintlig funktion och välja en av bindningarna som beskrivs i den här artikeln.

I båda fallen visas en varning som anger vilket tillägg som ska installeras. Klicka på **Installera** för att hämta tillägget. Varje tillägg behöver bara installeras en gång per funktionsapp. 

> [!Note] 
> Installationsprocessen i portalen kan ta upp till 10 minuter på en förbrukningsplan.

Om du använder Visual Studio kan du få tilläggen genom att installera [NuGet-paketen som visas tidigare i den här artikeln](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurera autentisering/auktorisering

Bindningarna som beskrivs i den här artikeln kräver att en identitet används. På så sätt kan Microsoft Graph framtvinga behörigheter och granskningsinteraktioner. Identiteten kan vara en användare som använder ditt program eller själva programmet. Konfigurera den här identiteten genom att konfigurera [apptjänstautentisering/auktorisering](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) med Azure Active Directory. Du måste också begära alla resursbehörigheter som dina funktioner kräver.

> [!Note] 
> Microsoft Graph-tillägget stöder endast Azure AD-autentisering. Användare måste logga in med ett arbets- eller skolkonto.

Om du använder Azure-portalen visas en varning under uppmaningen att installera tillägget. Varningen uppmanar dig att konfigurera App Service autentisering / auktorisering och begära eventuella behörigheter mallen eller bindningen kräver. Klicka på **Konfigurera Azure AD nu** eller Lägg till **behörigheter nu** efter behov.



<a name="token-input"></a>
## <a name="auth-token"></a>Auth-token

Auth token indatabindning hämtar en Azure AD-token för en viss resurs och tillhandahåller den till din kod som en sträng. Resursen kan vara vilken resurs som helst som programmet har behörigheter för. 

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#auth-token---example)
* [Attribut](#auth-token---attributes)
* [Konfiguration](#auth-token---configuration)
* [Användning](#auth-token---usage)

### <a name="auth-token---example"></a>Auth token - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#auth-token---c-script-example)
* [Javascript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth token - C# skript exempel

Följande exempel hämtar information om användarprofiler.

*Filen function.json* definierar en HTTP-utlösare med en tokenindatabindning:

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

C#-skriptkoden använder token för att ringa ett HTTP-anrop till Microsoft Graph och returnerar resultatet:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Auth token - JavaScript exempel

Följande exempel hämtar information om användarprofiler.

*Filen function.json* definierar en HTTP-utlösare med en tokenindatabindning:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript-koden använder token för att ringa ett HTTP-anrop till Microsoft Graph och returnerar resultatet.

```js
const rp = require('request-promise');

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

### <a name="auth-token---attributes"></a>Auth token - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [attributet Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Auth token - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Token` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för auth-token. Se [Använda en auth token indatabindning från kod](#token-input-code).|
|**Typ**| Saknas |Obligatoriskt - måste `token`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `in`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid**|**Userid**  |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |
|**Resurs**|**Resurs**|Obligatoriskt - En Azure AD-resurs-URL som token begärs för.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth token - användning

Bindningen i sig kräver inga Azure AD-behörigheter, men beroende på hur token används kan du behöva begära ytterligare behörigheter. Kontrollera kraven för den resurs som du tänker komma åt med token.

Token visas alltid för kod som en sträng.

> [!Note]
> När du utvecklar lokalt `userFromId` `userFromToken` med `userFromRequest` någon av , eller alternativ, kan `X-MS-TOKEN-AAD-ID-TOKEN` obligatorisk token [erhållas manuellt](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) och anges i begäran huvudet från ett anropande klientprogram.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-indata

Excel-tabellindatabindningen läser innehållet i en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-input---example)
* [Attribut](#excel-input---attributes)
* [Konfiguration](#excel-input---configuration)
* [Användning](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-indata - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#excel-input---c-script-example)
* [Javascript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Exempel på Excel-indata - C#-skript

Följande *function.json-fil* definierar en HTTP-utlösare med en Excel-indatabindning:

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

Följande C#-skriptkod läser innehållet i den angivna tabellen och returnerar dem till användaren:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel-indata - JavaScript-exempel

Följande *function.json-fil* definierar en HTTP-utlösare med en Excel-indatabindning:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande JavaScript-kod läser innehållet i den angivna tabellen och returnerar dem till användaren.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel-indata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [Attributet Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Excel-indata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Excel` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för Excel-tabellen. Se [Använda en Excel-tabellinmatningsbindning från kod](#excel-input-code).|
|**Typ**| Saknas |Obligatoriskt - måste `excel`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `in`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid**|**Userid**  |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |
|**Sökvägen**|**Sökväg**|Obligatoriskt – sökvägen i OneDrive till Excel-arbetsboken.|
|**kalkylbladNamn**|**Kalkylbladsnamn**|Kalkylbladet där tabellen hittas.|
|**Tablename**|**Tablename**|Namnet på tabellen. Om inget anges används innehållet i kalkylbladet.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-indata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läsa användarfiler|

Bindningen exponerar följande typer för .NET-funktioner:
- sträng[][]
- Microsoft.Graph.Arbetsbokstabell
- Anpassade objekttyper (med strukturell modellbindning)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-utdata

Excel-utdatabindningen ändrar innehållet i en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-output---example)
* [Attribut](#excel-output---attributes)
* [Konfiguration](#excel-output---configuration)
* [Användning](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-utdata - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#excel-output---c-script-example)
* [Javascript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Exempel på Excel-utdata - C#-skript

I följande exempel läggs rader till i en Excel-tabell.

*Filen function.json* definierar en HTTP-utlösare med en Excel-utdatabindning:

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

C#-skriptkoden lägger till en ny rad i tabellen (antas vara enkolumn) baserat på indata från frågesträngen:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
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

#### <a name="excel-output---javascript-example"></a>Excel-utdata - JavaScript-exempel

I följande exempel läggs rader till i en Excel-tabell.

*Filen function.json* definierar en HTTP-utlösare med en Excel-utdatabindning:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande JavaScript-kod lägger till en ny rad i tabellen (antas vara enkolumn) baserat på indata från frågesträngen.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel-utdata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [Attributet Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Excel-utdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Excel` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för auth-token. Se [Använda en Excel-tabellutdatabindning från kod](#excel-output-code).|
|**Typ**| Saknas |Obligatoriskt - måste `excel`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `out`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid** |**Userid** |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |
|**Sökvägen**|**Sökväg**|Obligatoriskt – sökvägen i OneDrive till Excel-arbetsboken.|
|**kalkylbladNamn**|**Kalkylbladsnamn**|Kalkylbladet där tabellen hittas.|
|**Tablename**|**Tablename**|Namnet på tabellen. Om inget anges används innehållet i kalkylbladet.|
|**updateType (uppdateringstyp)**|**UpdateType (Olika)**|Obligatoriskt - Den typ av ändring som ska ändras i tabellen. Kan vara något av följande värden:<ul><li><code>update</code>- Ersätter innehållet i tabellen i OneDrive.</li><li><code>append</code>- Lägger till nyttolasten i slutet av tabellen i OneDrive genom att skapa nya rader.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel-utdata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen exponerar följande typer för .NET-funktioner:
- sträng[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.Arbetsbokstabell
- Anpassade objekttyper (med strukturell modellbindning)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Indata för fil

Indatabindningen för OneDrive-fil läser innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-input---example)
* [Attribut](#file-input---attributes)
* [Konfiguration](#file-input---configuration)
* [Användning](#file-input---usage)

### <a name="file-input---example"></a>Indata för fil - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#file-input---c-script-example)
* [Javascript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Filinmatning - C#-skriptexempel

I följande exempel står det en fil som lagras i OneDrive.

*Filen function.json* definierar en HTTP-utlösare med en OneDrive-indatabindning:

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

C#-skriptkoden läser filen som anges i frågesträngen och loggar dess längd:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Filinmatning - JavaScript-exempel

I följande exempel står det en fil som lagras i OneDrive.

*Filen function.json* definierar en HTTP-utlösare med en OneDrive-indatabindning:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande JavaScript-kod läser filen som anges i frågesträngen och returnerar dess längd.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Indata för fil - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Indata för fil - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `OneDrive` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för filen. Se [Använda en OneDrive-indatabindning från kod](#onedrive-input-code).|
|**Typ**| Saknas |Obligatoriskt - måste `onedrive`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `in`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid**|**Userid**  |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |
|**Sökvägen**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till filen.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Indata för fil - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läsa användarfiler|

Bindningen exponerar följande typer för .NET-funktioner:
- byte[]
- Strömma
- sträng
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Filutdata

OneDrive-filutdatabindningen ändrar innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-output---example)
* [Attribut](#file-output---attributes)
* [Konfiguration](#file-output---configuration)
* [Användning](#file-output---usage)

### <a name="file-output---example"></a>Filutdata - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#file-output---c-script-example)
* [Javascript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Filutdata - C#-skriptexempel

I följande exempel skrivs till en fil som lagras i OneDrive.

*Filen function.json* definierar en HTTP-utlösare med onedrive-utdatabindning:

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

C#-skriptkoden hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt enligt definitionen i föregående exempel) vid roten på anroparens OneDrive:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Filutdata - JavaScript-exempel

I följande exempel skrivs till en fil som lagras i OneDrive.

*Filen function.json* definierar en HTTP-utlösare med onedrive-utdatabindning:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript-koden hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt enligt definitionen i config ovan) vid roten på anroparens OneDrive.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Filutdata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Filutdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `OneDrive` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för filen. Se [Använda en OneDrive-filutdatabindning från kod](#onedrive-output-code).|
|**Typ**| Saknas |Obligatoriskt - måste `onedrive`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `out`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid** |**Userid** |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |
|**Sökvägen**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till filen.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Filutdata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen exponerar följande typer för .NET-funktioner:
- byte[]
- Strömma
- sträng
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-utdata

Meddelandemeddelandebindningen skickar ett e-postmeddelande via Outlook.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#outlook-output---example)
* [Attribut](#outlook-output---attributes)
* [Konfiguration](#outlook-output---configuration)
* [Användning](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook-utdata - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#outlook-output---c-script-example)
* [Javascript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook-utdata - C#-skriptexempel

I följande exempel skickas ett e-postmeddelande via Outlook.

*Filen function.json* definierar en HTTP-utlösare med en Outlook-meddelandeutdatabindning:

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

C#-skriptkoden skickar ett e-postmeddelande från anroparen till en mottagare som anges i frågesträngen:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
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

#### <a name="outlook-output---javascript-example"></a>Outlook-utdata - JavaScript-exempel

I följande exempel skickas ett e-postmeddelande via Outlook.

*Filen function.json* definierar en HTTP-utlösare med en Outlook-meddelandeutdatabindning:

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

JavaScript-koden skickar ett e-postmeddelande från anroparen till en mottagare som anges i frågesträngen:

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

### <a name="outlook-output---attributes"></a>Outlook-utdata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [Attributet Outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Outlook-utdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Outlook` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för e-postmeddelandet. Se [Använda en Outlook-meddelandeutdatabindning från kod](#outlook-output-code).|
|**Typ**| Saknas |Obligatoriskt - måste `outlook`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `out`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid**|**Userid**  |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-utdata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Skicka e-post som användare|

Bindningen exponerar följande typer för .NET-funktioner:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- sträng
- Anpassade objekttyper (med strukturell modellbindning)






## <a name="webhooks"></a>Webhooks

Med Webhooks kan du reagera på händelser i Microsoft Graph. För att stödja webhooks behövs funktioner för att skapa, uppdatera och reagera på _webhook-prenumerationer_. En komplett webhook-lösning kräver en kombination av följande bindningar:
- Med en [Microsoft Graph webhook-utlösare](#webhook-trigger) kan du reagera på en inkommande webhook.
- Med en [Microsoft Graph webhook-prenumerationsbindning](#webhook-input) kan du lista befintliga prenumerationer och eventuellt uppdatera dem.
- Med en [Microsoft Graph webhook-prenumerationsbindning](#webhook-output) kan du skapa eller ta bort webhook-prenumerationer.

Bindningarna själva kräver inga Azure AD-behörigheter, men du måste begära behörigheter som är relevanta för den resurstyp du vill reagera på. En lista över vilka behörigheter som behövs för varje resurstyp finns i [prenumerationsbehörigheter](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Mer information om webhooks finns [i Arbeta med webhooks i Microsoft Graph].





## <a name="webhook-trigger"></a>Webhook-utlösare

Microsoft Graph webhook-utlösaren gör att en funktion kan reagera på en inkommande webhook från Microsoft Graph. Varje instans av den här utlösaren kan reagera på en resurstyp för Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-trigger---example)
* [Attribut](#webhook-trigger---attributes)
* [Konfiguration](#webhook-trigger---configuration)
* [Användning](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-utlösare - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#webhook-trigger---c-script-example)
* [Javascript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook-utlösare - C#-skriptexempel

I följande exempel hanteras webhooks för inkommande Outlook-meddelanden. Om du vill använda en webhook-utlösare skapar du [en prenumeration](#webhook-output---example)och du kan [uppdatera prenumerationen](#webhook-subscription-refresh) så att den inte upphör att gälla.

*Filen function.json* definierar en webhook-utlösare:

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

C#-skriptkoden reagerar på inkommande e-postmeddelanden och loggar brödtexten för dem som skickas av mottagaren och som innehåller "Azure Functions" i ämnet:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhook-utlösare - JavaScript-exempel

I följande exempel hanteras webhooks för inkommande Outlook-meddelanden. Om du vill använda en webhook-utlösare skapar du [en prenumeration](#webhook-output---example)och du kan [uppdatera prenumerationen](#webhook-subscription-refresh) så att den inte upphör att gälla.

*Filen function.json* definierar en webhook-utlösare:

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

JavaScript-koden reagerar på inkommande e-postmeddelanden och loggar brödtexten för dem som skickas av mottagaren och som innehåller "Azure Functions" i ämnet:

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

### <a name="webhook-trigger---attributes"></a>Webhook-utlösare - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Webhook-utlösare - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `GraphWebhookTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för e-postmeddelandet. Se [Använda en Outlook-meddelandeutdatabindning från kod](#outlook-output-code).|
|**Typ**| Saknas |Obligatoriskt - måste `graphWebhook`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `trigger`ställas in på .|
|**Resurstypens**|**ResourceType**|Obligatoriskt - grafresursen som den här funktionen ska svara på webhooks. Kan vara något av följande värden:<ul><li><code>#Microsoft.Graph.Message</code>- ändringar som gjorts i Outlook-meddelanden.</li><li><code>#Microsoft.Graph.DriveItem</code>- Ändringar som gjorts i OneDrive-rotobjekt.</li><li><code>#Microsoft.Graph.Contact</code>- ändringar som gjorts i personliga kontakter i Outlook.</li><li><code>#Microsoft.Graph.Event</code>- ändringar som gjorts i Outlook-kalenderobjekt.</li></ul>|

> [!Note]
> En funktionsapp kan bara ha en `resourceType` funktion som är registrerad mot ett visst värde.

### <a name="webhook-trigger---usage"></a>Webhook-utlösare - användning

Bindningen exponerar följande typer för .NET-funktioner:
- Microsoft Graph SDK-typer som är `Microsoft.Graph.Message` relevanta `Microsoft.Graph.DriveItem`för resurstypen, till exempel eller .
- Anpassade objekttyper (med strukturell modellbindning)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook-inmatning

Med indatabindningen för Microsoft Graph kan du hämta listan över prenumerationer som hanteras av den här funktionsappen. Bindningen läser från funktionsapplagring, så den återspeglar inte andra prenumerationer som skapats utanför appen.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-input---example)
* [Attribut](#webhook-input---attributes)
* [Konfiguration](#webhook-input---configuration)
* [Användning](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook-indata - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#webhook-input---c-script-example)
* [Javascript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook-indata - C#-skriptexempel

I följande exempel hämtar alla prenumerationer för den uppringande användaren och tar bort dem.

*Filen function.json* definierar en HTTP-utlösare med en prenumerationsindatabindning och en prenumerationsutdatabindning som använder borttagningsåtgärden:

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

C#-skriptkoden hämtar prenumerationerna och tar bort dem:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhook-indata - JavaScript-exempel

I följande exempel hämtar alla prenumerationer för den uppringande användaren och tar bort dem.

*Filen function.json* definierar en HTTP-utlösare med en prenumerationsindatabindning och en prenumerationsutdatabindning som använder borttagningsåtgärden:

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

JavaScript-koden hämtar prenumerationerna och tar bort dem:

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

### <a name="webhook-input---attributes"></a>Webhook-indata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Webhook-indata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `GraphWebhookSubscription` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för e-postmeddelandet. Se [Använda en Outlook-meddelandeutdatabindning från kod](#outlook-output-code).|
|**Typ**| Saknas |Obligatoriskt - måste `graphWebhookSubscription`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `in`ställas in på .|
|**Filter**|**Filter**| Om den `userFromRequest`är inställd på hämtas endast prenumerationer som ägs av den uppringande användaren (gäller endast med [HTTP-utlösare).]| 

### <a name="webhook-input---usage"></a>Webhook-inmatning - användning

Bindningen exponerar följande typer för .NET-funktioner:
- sträng[]
- Anpassade objekttypsmatriser
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Prenumeration[]





## <a name="webhook-output"></a>Webhook-utgång

Med webhook-prenumerationsbindningen kan du skapa, ta bort och uppdatera webhook-prenumerationer i Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-output---example)
* [Attribut](#webhook-output---attributes)
* [Konfiguration](#webhook-output---configuration)
* [Användning](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook-utgång - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#webhook-output---c-script-example)
* [Javascript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook-utgång - C#-skriptexempel

I följande exempel skapas en prenumeration. Du kan [uppdatera prenumerationen](#webhook-subscription-refresh) så att den inte upphör att gälla.

*Filen function.json* definierar en HTTP-utlösare med en prenumerationsutdatabindning med hjälp av åtgärden skapa:

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
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
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

C#-skriptkoden registrerar en webhook som meddelar den här funktionsappen när den uppringande användaren får ett Outlook-meddelande:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhook-utgång - JavaScript-exempel

I följande exempel skapas en prenumeration. Du kan [uppdatera prenumerationen](#webhook-subscription-refresh) så att den inte upphör att gälla.

*Filen function.json* definierar en HTTP-utlösare med en prenumerationsutdatabindning med hjälp av åtgärden skapa:

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
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
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

JavaScript-koden registrerar en webhook som meddelar den här funktionsappen när den uppringande användaren får ett Outlook-meddelande:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook-utdata – attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Webhook-utdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `GraphWebhookSubscription` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Namn**| Saknas |Obligatoriskt - variabelnamnet som används i funktionskoden för e-postmeddelandet. Se [Använda en Outlook-meddelandeutdatabindning från kod](#outlook-output-code).|
|**Typ**| Saknas |Obligatoriskt - måste `graphWebhookSubscription`ställas in på .|
|**riktning**| Saknas |Obligatoriskt - måste `out`ställas in på .|
|**Identitet**|**Identitet**|Obligatoriskt - Den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code>- Gäller endast med [HTTP-utlösare]. Använder den uppringande användarens identitet.</li><li><code>userFromId</code>- Använder identiteten för en tidigare inloggad användare med det angivna ID:t. Se <code>userId</code> fastigheten.</li><li><code>userFromToken</code>- Använder identiteten som representeras av den angivna token. Se <code>userToken</code> fastigheten.</li><li><code>clientCredentials</code>- Använder funktionsappens identitet.</li></ul>|
|**Userid**|**Userid**  |Behövs om och endast om `userFromId` _identiteten_ är inställd på . Ett användarhuvudtillstånds-ID som är associerat med en tidigare inloggad användare.|
|**användareToken**|**AnvändareToken**|Behövs om och endast om `userFromToken` _identiteten_ är inställd på . En token som är giltig för funktionsappen. |
|**Åtgärder**|**Åtgärd**|Obligatoriskt - anger vilken åtgärd bindningen ska utföra. Kan vara något av följande värden:<ul><li><code>create</code>- Registrerar en ny prenumeration.</li><li><code>delete</code>- Tar bort en angiven prenumeration.</li><li><code>refresh</code>- Uppdaterar en angiven prenumeration för att förhindra att den upphör att gälla.</li></ul>|
|**subscriptionResource**|**PrenumerationResource**|Behövs om och _action_ endast om `create`åtgärden är inställd på . Anger den Microsoft Graph-resurs som ska övervakas för ändringar. Se [Arbeta med webhooks i Microsoft Graph]. |
|**Changetype**|**Changetype**|Behövs om och _action_ endast om `create`åtgärden är inställd på . Anger vilken typ av ändring i den prenumererade resursen som höjer ett meddelande. De värden som `created` `updated`stöds `deleted`är: , , . Flera värden kan kombineras med hjälp av en kommaavgränsad lista.|

### <a name="webhook-output---usage"></a>Webhook-utdata - användning

Bindningen exponerar följande typer för .NET-funktioner:
- sträng
- Microsoft.Graph.Prenumeration




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Uppdatering av Webhook-prenumeration

Det finns två sätt att uppdatera prenumerationer:

- Använd programidentiteten för att hantera alla prenumerationer. Detta kräver medgivande från en Azure Active Directory-administratör. Detta kan användas av alla språk som stöds av Azure Functions.
- Använd identiteten som är associerad med varje prenumeration genom att manuellt binda varje användar-ID. Detta kräver en del anpassad kod för att utföra bindningen. Detta kan endast användas av .NET-funktioner.

Det här avsnittet innehåller ett exempel för var och en av dessa metoder:

* [Exempel på appidentitet](#webhook-subscription-refresh---app-identity-example)
* [Exempel på användaridentitet](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Uppdatering av Webhook-prenumeration – exempel på appidentitet

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>App identitetsuppdatering - C# skript exempel

I följande exempel används programidentiteten för att uppdatera en prenumeration.

*Function.json* definierar en timerutlösare med en prenumerationsindatabindning och en prenumerationsutdatabindning:

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

C#-skriptkoden uppdaterar prenumerationerna:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>App identitetsuppdatering - C# skript exempel

I följande exempel används programidentiteten för att uppdatera en prenumeration.

*Function.json* definierar en timerutlösare med en prenumerationsindatabindning och en prenumerationsutdatabindning:

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

JavaScript-koden uppdaterar prenumerationerna:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Uppdatering av Webhook-prenumeration – exempel på användaridentitet

I följande exempel används användaridentiteten för att uppdatera en prenumeration.

*Filen function.json* definierar en timerutlösare och skjuter upp prenumerationsindatabindningen till funktionskoden:

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

C#-skriptkoden uppdaterar prenumerationerna och skapar utdatabindningen i kod med hjälp av varje användares identitet:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
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
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)

[HTTP-utlösare]: functions-bindings-http-webhook.md
[Arbeta med webhooks i Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
