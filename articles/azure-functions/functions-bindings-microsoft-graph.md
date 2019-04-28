---
title: Microsoft Graph-bindningar för Azure Functions
description: Förstå hur du använder Microsoft Graph-utlösare och bindningar i Azure Functions.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: f112bdf9eacf51852659ab49a5673b0c8bfb0e46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438116"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph-bindningar för Azure Functions

Den här artikeln förklarar hur du konfigurerar och arbetar med Microsoft Graph-utlösare och bindningar i Azure Functions. Med dessa kan du använda Azure Functions för att arbeta med data, insikter och händelser från den [Microsoft Graph](https://developer.microsoft.com/graph).

Microsoft Graph-tillägget innehåller följande bindningarna:
- En [autentiseringstoken indatabindning](#token-input) kan du samverka med Microsoft Graph API: er.
- En [Excel-tabell indatabindning](#excel-input) kan du läsa data från Excel.
- En [Excel-tabell utdatabindning](#excel-output) kan du ändra Excel-data.
- En [OneDrive-fil indatabindning](#onedrive-input) gör att du kan läsa filer från OneDrive.
- En [OneDrive-fil på en utgående bindning](#onedrive-output) gör att du kan skriva till filer i OneDrive.
- En [postmeddelande i Outlook-utdatabindning](#outlook-output) kan du skicka e-post via Outlook.
- En samling [Microsoft Graph-webhook-utlösare och bindningar](#webhooks) kan du reagera på händelser från Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph-bindningar finns för närvarande i förhandsversion för Azure Functions version 2.x. De stöds inte i Functions version 1.x.

## <a name="packages"></a>Paket

Auth-token indatabindningen finns i den [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet-paketet. Med Microsoft Graph-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) paketet. Källkoden för paket som finns i den [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Konfigurera tillägg

Microsoft Graph-bindningar som är tillgängliga via _bindande tillägg_. Tillägg av bindning är valfria komponenter till Azure Functions-körningen. Det här avsnittet visar hur du konfigurerar Microsoft Graph och tillägg för auth-token.

### <a name="enabling-functions-20-preview"></a>Aktiverar funktioner 2.0 preview

Tillägg av bindning är endast tillgängliga för Azure Functions 2.0 preview. 

Information om hur du anger en funktionsapp som använder funktionskörningen förhandsversion 2.0 finns i [hur du Azure Functions runtime versioner](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installationen av tillägget

Om du vill installera ett tillägg från Azure-portalen, går du till en mall eller bindning som refererar till den. Skapa en ny funktion och välja ”Microsoft Graph”-scenario i urvalsskärm mall. Välj en av mallarna från det här scenariot. Du kan också gå till fliken ”integrera” i en befintlig funktion och välja en av de bindningar som beskrivs i den här artikeln.

I båda fallen visas en varning som anger tillägget installeras. Klicka på **installera** att hämta tillägget. Varje tillägg behöver bara installeras en gång per funktionsapp. 

> [!Note] 
> I portalen installationen kan ta upp till 10 minuter i en förbrukningsplan.

Om du använder Visual Studio kan du hämta tillägg genom att installera [NuGet-paket som anges tidigare i den här artikeln](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurera autentisering / auktorisering

Bindningar som beskrivs i den här artikeln kräver en identitet som ska användas. På så sätt kan Microsoft Graph att tillämpa behörigheter och granska interaktioner. Identiteten kan vara en användare med åtkomst till programmet eller själva programmet. Om du vill konfigurera den här identiteten, ställa in [App Service-autentisering / auktorisering](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) med Azure Active Directory. Du måste också att begära valfri resursbehörighet som dina funktioner kräver.

> [!Note] 
> Microsoft Graph-tillägget har endast stöd för Azure AD-autentisering. Användarna måste logga in med ett arbets- eller skolkonto.

Om du använder Azure-portalen, visas en varning som visas nedan prompten för att installera tillägget. Varningen uppmanar dig att konfigurera App Service-autentisering / auktorisering och begäran kräver att alla behörigheter mall eller bindning. Klicka på **konfigurera Azure AD nu** eller **Lägg till behörigheter nu** efter behov.



<a name="token-input"></a>
## <a name="auth-token"></a>Autentiseringstoken

Auth-token indatabindningen hämtar en Azure AD-token för en viss resurs och ger den i koden som en sträng. Resursen kan vara någon som har behörigheter för programmet. 

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#auth-token---example)
* [Attribut](#auth-token---attributes)
* [Konfiguration](#auth-token---configuration)
* [Användning](#auth-token---usage)

### <a name="auth-token---example"></a>Auth-token - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth-token - exempel på C#-skript

I följande exempel hämtar information om användarprofiler.

Den *function.json* fil definierar en HTTP-utlösare med en token indatabindning:

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

C#-skriptkoden använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet:

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

#### <a name="auth-token---javascript-example"></a>Auth-token - JavaScript-exempel

I följande exempel hämtar information om användarprofiler.

Den *function.json* fil definierar en HTTP-utlösare med en token indatabindning:

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

JavaScript-koden använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet.

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

### <a name="auth-token---attributes"></a>Auth-token - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attribut.

### <a name="auth-token---configuration"></a>Auth-token - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Token` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för auth-token. Se [med hjälp av en autentiseringstoken indatabindning från kod](#token-input-code).|
|**typ**||Krävs – måste vara inställd på `token`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |
|**Resurs**|**resource**|Krävs – en Azure AD-resurs-URL som token begärs.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth-token - användning

Bindningen själva kräver inte några Azure AD-behörigheter, men beroende på hur token används, kan du behöva begära ytterligare behörighet. Kontrollera kraven för den resurs som du vill komma åt med token.

Token visas alltid för kod som en sträng.

> [!Note]
> När du utvecklar lokalt med något av `userFromId`, `userFromToken` eller `userFromRequest` alternativ, som kan vara nödvändiga token [fick manuellt](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) och anges i `X-MS-TOKEN-AAD-ID-TOKEN` huvudet i begäran från en anropande klientprogram.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-indata

Excel-tabell indatabindning läser innehåll från en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-input---example)
* [Attribut](#excel-input---attributes)
* [Konfiguration](#excel-input---configuration)
* [Användning](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-indata - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel indata - exempel på C#-skript

Följande *function.json* fil definierar en HTTP-utlösare med en Excel-indatabindning:

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

I följande C#-skriptkoden läser innehåll från den angivna tabellen och returnerar dem till användaren:

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

#### <a name="excel-input---javascript-example"></a>Indata - Excel JavaScript-exempel

Följande *function.json* fil definierar en HTTP-utlösare med en Excel-indatabindning:

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

Följande JavaScript-kod läser innehåll från den angivna tabellen och returnerar dem till användaren.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribut.

### <a name="excel-input---configuration"></a>Excel-indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Excel` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för Excel-tabellen. Se [med hjälp av en Excel-tabell indatabindning från kod](#excel-input-code).|
|**typ**||Krävs – måste vara inställd på `excel`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |
|**path**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|**WorksheetName**|Kalkylbladet där tabellen finns.|
|**tableName**|**TableName**|Tabellens namn. Om den inte anges används innehållet i kalkylbladet.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel indata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läs användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- string[][]
- Microsoft.Graph.WorkbookTable
- Anpassade objektgrupper (med strukturella modellen bindning)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-utdata

Excel utdatabindning ändrar innehållet i en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-output---example)
* [Attribut](#excel-output---attributes)
* [Konfiguration](#excel-output---configuration)
* [Användning](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-utdata - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel utdata - exempel på C#-skript

I följande exempel lägger till rader till en Excel-tabell.

Den *function.json* fil definierar en HTTP-utlösare med en Excel-utdatabindning:

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

C#-skriptkoden lägger till en ny rad i tabellen (antas vara kolumn) baserat på indata från frågesträngen:

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

#### <a name="excel-output---javascript-example"></a>Utdata - Excel JavaScript-exempel

I följande exempel lägger till rader till en Excel-tabell.

Den *function.json* fil definierar en HTTP-utlösare med en Excel-utdatabindning:

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

Följande JavaScript-kod lägger till en ny rad i tabellen (antas vara kolumn) baserat på indata från frågesträngen.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribut.

### <a name="excel-output---configuration"></a>Excel-utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Excel` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för auth-token. Se [med hjälp av en Excel-tabell utdatabindning från kod](#excel-output-code).|
|**typ**||Krävs – måste vara inställd på `excel`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID** |**Användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |
|**path**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|**WorksheetName**|Kalkylbladet där tabellen finns.|
|**tableName**|**TableName**|Tabellens namn. Om den inte anges används innehållet i kalkylbladet.|
|**updateType**|**Uppdateringstyp**|Krävs – typ av ändring som ska göras i tabellen. Kan vara något av följande värden:<ul><li><code>update</code> -Ersätter innehållet i tabellen i OneDrive.</li><li><code>append</code> -Lägger till nyttolasten i slutet av tabellen i OneDrive genom att skapa nya rader.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel utdata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Anpassade objektgrupper (med strukturella modellen bindning)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Fil som indata

OneDrive-fil indatabindning läser innehåll från en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-input---example)
* [Attribut](#file-input---attributes)
* [Konfiguration](#file-input---configuration)
* [Användning](#file-input---usage)

### <a name="file-input---example"></a>Filen indata - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Indata - filen exempel på C#-skript

I följande exempel läser en fil som lagras i OneDrive.

Den *function.json* fil definierar en HTTP-utlösare med en indatabindning för OneDrive-fil:

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

#### <a name="file-input---javascript-example"></a>Indata - filen JavaScript-exempel

I följande exempel läser en fil som lagras i OneDrive.

Den *function.json* fil definierar en HTTP-utlösare med en indatabindning för OneDrive-fil:

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

Följande JavaScript-kod läser filen som anges i frågesträngen och returnerar längden.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Filen indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribut.

### <a name="file-input---configuration"></a>Filen indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `OneDrive` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för filen. Se [med hjälp av en fil i OneDrive indatabindning från kod](#onedrive-input-code).|
|**typ**||Krävs – måste vara inställd på `onedrive`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |
|**path**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till filen.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Filen indata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läs användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- byte
- Strömma
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Fil-utdata

OneDrive-fil utdatabindning ändrar innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-output---example)
* [Attribut](#file-output---attributes)
* [Konfiguration](#file-output---configuration)
* [Användning](#file-output---usage)

### <a name="file-output---example"></a>Filen utdata - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Utdata - filen exempel på C#-skript

I följande exempel skriver till en fil som lagras i OneDrive.

Den *function.json* fil definierar en HTTP-utlösare med en OneDrive-utdatabindning:

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

C#-skriptkoden hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt som definierats i föregående exempel) i roten på anroparens OneDrive:

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

#### <a name="file-output---javascript-example"></a>Utdata - filen JavaScript-exempel

I följande exempel skriver till en fil som lagras i OneDrive.

Den *function.json* fil definierar en HTTP-utlösare med en OneDrive-utdatabindning:

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

JavaScript-koden hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt enligt definitionerna i konfigurationen ovan) i roten på anroparens OneDrive.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Filen utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribut.

### <a name="file-output---configuration"></a>Filen utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `OneDrive` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för filen. Se [med hjälp av en fil i OneDrive-utdatabindning från kod](#onedrive-output-code).|
|**typ**||Krävs – måste vara inställd på `onedrive`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID** |**Användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |
|**path**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till filen.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Filen utdata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- byte
- Strömma
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-utdata

Outlook-meddelande utdata bindningen skickar ett e-postmeddelande via Outlook.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#outlook-output---example)
* [Attribut](#outlook-output---attributes)
* [Konfiguration](#outlook-output---configuration)
* [Användning](#outlook-output---usage)

### <a name="outlook-output---example"></a>Utdata för Outlook - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook utdata - exempel på C#-skript

I följande exempel skickar ett e-post via Outlook.

Den *function.json* fil definierar en HTTP-utlösare med ett Outlook utdatabindning för meddelandet:

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

C#-skriptkoden skickar ett e-postmeddelande från anroparen till en mottagare som angetts i frågesträngen:

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

#### <a name="outlook-output---javascript-example"></a>Outlook utdata - JavaScript-exempel

I följande exempel skickar ett e-post via Outlook.

Den *function.json* fil definierar en HTTP-utlösare med ett Outlook utdatabindning för meddelandet:

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

JavaScript-koden skickar ett e-postmeddelande från anroparen till en mottagare som angetts i frågesträngen:

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

### <a name="outlook-output---attributes"></a>Outlook utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) attribut.

### <a name="outlook-output---configuration"></a>Utdata för Outlook - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Outlook` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för e-postmeddelande. Se [med hjälp av ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `outlook`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook utdata - användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Skicka e-post som användare|

Bindningen visar följande typer till .NET-funktioner:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Anpassade objektgrupper (med strukturella modellen bindning)






## <a name="webhooks"></a>Webhooks

Webhooks kan du reagera på händelser i Microsoft Graph. För att stödja webhooks, funktioner som krävs för att skapa, uppdatera och ta hänsyn till _webhook prenumerationer_. En fullständig webhook-lösning kräver en kombination av bindningarna som följande:
- En [Microsoft Graph-webhook-utlösaren](#webhook-trigger) kan du reagera på en inkommande webhook.
- En [webhook-prenumeration för Microsoft Graph indatabindning](#webhook-input) kan du lista över befintliga prenumerationer och du kan också uppdatera dem.
- En [webhook-prenumeration för Microsoft Graph-utdatabindning](#webhook-output) kan du skapa eller ta bort webhook-prenumerationer.

Bindningarna själva kräver inte några Azure AD-behörigheter, men du måste begära behörighet som är relevanta för den resurstyp som du vill ta hänsyn till. En lista av vilka behörigheter som krävs för varje resurstyp finns i [Prenumerationsbehörigheter](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Läs mer om webhooks [arbeta med webhooks i Microsoft Graph].





## <a name="webhook-trigger"></a>Webhook-utlösaren

Microsoft Graph-webhook-utlösaren kan en funktion som ska reagera på en inkommande webhook från Microsoft Graph. Varje instans av den här utlösaren kan reagera på en resurstyp för Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-trigger---example)
* [Attribut](#webhook-trigger---attributes)
* [Konfiguration](#webhook-trigger---configuration)
* [Användning](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-utlösaren - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook-utlösaren – exempel på C#-skript

I följande exempel hanterar webhooks för inkommande Outlook-meddelanden. Du använder en webhook utlöser du [skapar du en prenumeration](#webhook-output---example), och du kan [uppdatera prenumerationen](#webhook-subscription-refresh) förhindra att den upphör att gälla.

Den *function.json* fil definierar en webhooksutlösare:

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

C#-skriptkoden reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och som innehåller ”Azure Functions” i ämnesraden:

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook-utlösaren – JavaScript-exempel

I följande exempel hanterar webhooks för inkommande Outlook-meddelanden. Du använder en webhook utlöser du [skapar du en prenumeration](#webhook-output---example), och du kan [uppdatera prenumerationen](#webhook-subscription-refresh) förhindra att den upphör att gälla.

Den *function.json* fil definierar en webhooksutlösare:

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

JavaScript-koden reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och som innehåller ”Azure Functions” i ämnesraden:

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

### <a name="webhook-trigger---attributes"></a>Webhook-utlösaren - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) attribut.

### <a name="webhook-trigger---configuration"></a>Webhook-utlösaren - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `GraphWebHookTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för e-postmeddelande. Se [med hjälp av ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `graphWebhook`.|
|**riktning**||Krävs – måste vara inställd på `trigger`.|
|**ResourceType**|**ResourceType**|Krävs – graphresursen för vilken den här funktionen ska svara på webhookar. Kan vara något av följande värden:<ul><li><code>#Microsoft.Graph.Message</code> -ändringar som gjorts i Outlook-meddelanden.</li><li><code>#Microsoft.Graph.DriveItem</code> -ändrade objekt för OneDrive-rot.</li><li><code>#Microsoft.Graph.Contact</code> -ändringar som gjorts i personliga kontakter i Outlook.</li><li><code>#Microsoft.Graph.Event</code> -ändringar som gjorts i kalenderobjekt i Outlook.</li></ul>|

> [!Note]
> En funktionsapp kan bara ha en funktion som har registrerats mot en viss `resourceType` värde.

### <a name="webhook-trigger---usage"></a>Webhook-utlösaren - användning

Bindningen visar följande typer till .NET-funktioner:
- Microsoft Graph SDK skriver relevanta för resurstypen som `Microsoft.Graph.Message` eller `Microsoft.Graph.DriveItem`.
- Anpassade objektgrupper (med strukturella modellen bindning)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook-indata

Microsoft Graph-webhook indatabindning kan du hämta en lista över prenumerationer som hanteras av den här funktionsappen. Bindningen läser från funktionen applagring, så att den inte avspeglar andra prenumerationer som skapats från utanför appen.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-input---example)
* [Attribut](#webhook-input---attributes)
* [Konfiguration](#webhook-input---configuration)
* [Användning](#webhook-input---usage)

### <a name="webhook-input---example"></a>Indata för Webhook - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook indata - exempel på C#-skript

I följande exempel hämtar alla prenumerationer för den anropande användaren och tar bort dem.

Den *function.json* fil definierar en HTTP-utlösare med en indatabindning för prenumeration och en prenumeration utdatabindning som använder borttagningsåtgärden:

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

#### <a name="webhook-input---javascript-example"></a>Indata - Webhook JavaScript-exempel

I följande exempel hämtar alla prenumerationer för den anropande användaren och tar bort dem.

Den *function.json* fil definierar en HTTP-utlösare med en indatabindning för prenumeration och en prenumeration utdatabindning som använder borttagningsåtgärden:

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

### <a name="webhook-input---attributes"></a>Webhook indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribut.

### <a name="webhook-input---configuration"></a>Indata för Webhook - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `GraphWebHookSubscription` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för e-postmeddelande. Se [med hjälp av ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `graphWebhookSubscription`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**filter**|**Filter**| Om inställd `userFromRequest`, och sedan bindningen kommer bara att hämta prenumerationer som ägs av den anropande användaren (gäller endast med [HTTP-utlösare]).| 

### <a name="webhook-input---usage"></a>Webhook indata - användning

Bindningen visar följande typer till .NET-funktioner:
- string[]
- Anpassat objekt typ matriser
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook-utdata

Webhook-prenumeration utdatabindning gör att du kan skapa, ta bort och uppdatera webhook-prenumerationer i Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-output---example)
* [Attribut](#webhook-output---attributes)
* [Konfiguration](#webhook-output---configuration)
* [Användning](#webhook-output---usage)

### <a name="webhook-output---example"></a>Utdata för Webhook - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook utdata - exempel på C#-skript

I följande exempel skapas en prenumeration. Du kan [uppdatera prenumerationen](#webhook-subscription-refresh) förhindra att den upphör att gälla.

Den *function.json* fil definierar en HTTP-utlösare med en prenumeration utdatabindning med hjälp av åtgärden Skapa:

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

C#-skriptkoden registrerar en webhook som meddelar funktionsappen när den anropande användaren får ett Outlook-meddelande:

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

#### <a name="webhook-output---javascript-example"></a>Utdata - Webhook JavaScript-exempel

I följande exempel skapas en prenumeration. Du kan [uppdatera prenumerationen](#webhook-subscription-refresh) förhindra att den upphör att gälla.

Den *function.json* fil definierar en HTTP-utlösare med en prenumeration utdatabindning med hjälp av åtgärden Skapa:

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

JavaScript-koden registrerar en webhook som meddelar funktionsappen när den anropande användaren får ett Outlook-meddelande:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribut.

### <a name="webhook-output---configuration"></a>Utdata för Webhook - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `GraphWebHookSubscription` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Krävs – variabelnamnet som används i Funktionskoden för e-postmeddelande. Se [med hjälp av ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `graphWebhookSubscription`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Krävs – den identitet som används för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare inloggade användare med angivet ID. Se den <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Se den <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionsappen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användarens huvudnamn ID som är associerade med en tidigare inloggade användare.|
|**userToken**|**Metadatafältet**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionsappen. |
|**Åtgärd**|**Åtgärd**|Obligatoriskt – anger åtgärden som bindningen ska utföra. Kan vara något av följande värden:<ul><li><code>create</code> -Registrerar en ny prenumeration.</li><li><code>delete</code> – Tar bort en angiven prenumeration.</li><li><code>refresh</code> – Uppdaterar en angiven prenumeration så att den upphör att gälla.</li></ul>|
|**subscriptionResource**|**subscriptionResource**|Krävs om och bara om den _åtgärd_ är inställd på `create`. Anger Microsoft Graph-resurs som ska övervakas för ändringar. Se [arbeta med webhooks i Microsoft Graph]. |
|**changeType**|**changeType**|Krävs om och bara om den _åtgärd_ är inställd på `create`. Anger typen av ändring i den prenumererade resursen som skapar ett meddelande. Godkända värden är: `created`, `updated`, `deleted`. Du kan kombinera flera värden med hjälp av en kommaavgränsad lista.|

### <a name="webhook-output---usage"></a>Webhook utdata - användning

Bindningen visar följande typer till .NET-funktioner:
- string
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Uppdatering av Webhook-prenumeration

Det finns två sätt att uppdatera prenumerationer:

- Använd programidentiteten behöver bry dig om alla prenumerationer. Detta kräver godkännande från en Azure Active Directory-administratör. Detta kan användas av alla språk som stöds av Azure Functions.
- Använda det identitet som är associerade med varje prenumeration genom att manuellt binda varje användar-ID. Detta kräver vissa anpassad kod för att utföra bindningen. Detta kan endast användas av .NET-funktioner.

Det här avsnittet innehåller ett exempel för var och en av följande metoder:

* [Exempel på identitet](#webhook-subscription-refresh---app-identity-example)
* [Användarens identitet exempel](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Uppdatering av Webhook-prenumeration - identity-exemplet

Se exempel språkspecifika:

* [C#-skript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>App-uppdatering för identity - exempel på C#-skript

I följande exempel används programidentiteten för att uppdatera en prenumeration.

Den *function.json* definierar en timer som utlösare med en prenumeration indatabindning och en prenumeration på en utgående bindning:

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

### <a name="app-identity-refresh---c-script-example"></a>App-uppdatering för identity - exempel på C#-skript

I följande exempel används programidentiteten för att uppdatera en prenumeration.

Den *function.json* definierar en timer som utlösare med en prenumeration indatabindning och en prenumeration på en utgående bindning:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Uppdatering av Webhook-prenumeration - användarens identitet exempel

I följande exempel använder användar-ID för att uppdatera en prenumeration.

Den *function.json* filen definierar en timer som utlösare och skjuter upp prenumerationen indatabindning till Funktionskoden:

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

C#-skriptkoden uppdaterar prenumerationerna och skapar utdata-bindning i kod, med varje användares identitet:

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
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

[HTTP-utlösare]: functions-bindings-http-webhook.md
[arbeta med webhooks i Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
