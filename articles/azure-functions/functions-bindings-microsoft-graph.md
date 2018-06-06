---
title: Microsoft Graph bindningar för Azure Functions
description: Förstå hur du använder Microsoft Graph utlösare och bindningar i Azure Functions.
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 3b9a7d998e7153318b21adcada7c143b428e591f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724782"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph bindningar för Azure Functions

Den här artikeln beskriver hur du konfigurerar och arbetar med Microsoft Graph utlösare och bindningar i Azure Functions. Med dessa kan du använda Azure Functions för att arbeta med data, insikter och händelser från den [Microsoft Graph](https://graph.microsoft.io).

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
> Microsoft Graph-bindningar finns för närvarande i förhandsvisning för Azure Functions version 2.x. De stöds inte i funktioner version 1.x.

## <a name="packages"></a>Paket

Auth token indatabindning finns i den [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet-paketet. Andra Microsoft Graph-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) paketet. Källkoden för paket finns i den [azure-funktioner-microsoftgraph-tillägget](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Konfigurera tillägg

Microsoft Graph bindningar är tillgängliga via _bindning tillägg_. Bindningen tillägg är valfria komponenter till Azure Functions-runtime. Det här avsnittet visar hur du ställer in av Microsoft Graph och auth token tillägg.

### <a name="enabling-functions-20-preview"></a>Aktivera funktioner 2.0 preview

Bindningen tillägg är bara tillgängliga för förhandsversionen av Azure Functions 2.0. 

Information om hur du ställer in en funktionsapp att använda förhandsversionen 2.0 av Functions-runtime finns [så avsedda för Azure Functions-runtime versioner](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installationen av tillägget

Om du vill installera ett tillägg från Azure portal, navigerar du till en mall eller bindning som refererar till den. Skapa en ny funktion och välj ”Microsoft Graph”-scenario i urvalssidan mallen. Välj en av mallar från det här scenariot. Du kan också gå till fliken ”integrera” i en befintlig funktion och välja en av de bindningar som beskrivs i den här artikeln.

I båda fallen visas en varning som anger att tillägget ska kunna installeras. Klicka på **installera** att hämta tillägget. Varje tillägg behöver bara installeras en gång per funktionsapp. 

> [!Note] 
> I portalen-installationen kan ta upp till 10 minuter på en plan för användning.

Om du använder Visual Studio, kan du få tillägg genom att installera [NuGet-paketen som listas ovan](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurera autentisering / auktorisering

De bindningar som beskrivs i den här artikeln kräver en identitet som ska användas. På så sätt kan Microsoft Graph att tillämpa behörigheter och granska interaktioner. Identiteten kan vara en användare att komma åt programmet eller programmet. Om du vill konfigurera den här identiteten konfigurera [App Service autentisering / auktorisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) med Azure Active Directory. Du måste också begära alla dina funktioner kräver resursbehörigheter.

> [!Note] 
> Microsoft Graph filnamnstillägget har endast stöd för Azure AD-autentisering. Användarna måste logga in med ett arbets- eller skolkonto.

Om du använder Azure-portalen, visas en varning under uppmaningen att installera tillägget. Varningen uppmanar dig att konfigurera App Service-autentisering / auktorisering och begära alla behörigheter mallen eller bindning krävs. Klicka på **nu konfigurera Azure AD** eller **lägga till behörigheter nu** efter behov.



<a name="token-input"></a>
## <a name="auth-token"></a>Autentiseringstoken

Auth token indatabindning hämtar en Azure AD-token för en viss resurs och förser koden som en sträng. Resursen kan vara någon som har behörighet för programmet. 

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#auth-token---example)
* [Attribut](#auth-token---attributes)
* [Konfiguration](#auth-token---configuration)
* [Användning](#auth-token---usage)

### <a name="auth-token---example"></a>Token för auth - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token för auth - exempel på C#-skript

I följande exempel hämtas användarens profilinformation.

Den *function.json* filen definierar en HTTP-utlösare med en token indatabindning:

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

C#-skriptkod använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet:

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

#### <a name="auth-token---javascript-example"></a>Token för auth - JavaScript-exempel

I följande exempel hämtas användarens profilinformation.

Den *function.json* filen definierar en HTTP-utlösare med en token indatabindning:

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

JavaScript-kod använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet.

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

### <a name="auth-token---attributes"></a>Token för auth - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attribut.

### <a name="auth-token---configuration"></a>Token för auth - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Token` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för auth-token. Se [med hjälp av en token för auth inkommande bindningen från kod](#token-input-code).|
|**typ**||Krävs – måste vara inställd på `token`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**Resurs**|**Resursen**|Krävs – en Azure AD resurs-URL som token begärs.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token för auth - användning

Bindningen själva kräver inte några Azure AD-behörigheter, men beroende på hur token som används, kan du behöva begär ytterligare behörighet. Kontrollera kraven för den resurs som du vill komma åt med token.

Token visas alltid till kod som en sträng.




<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-indata

Excel-tabellen indatabindning läser innehållet i en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-input---example)
* [Attribut](#excel-input---attributes)
* [Konfiguration](#excel-input---configuration)
* [Användning](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-indata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel-indata - exempel på C#-skript

Följande *function.json* filen definierar en HTTP-utlösare med en Excel-indatabindning:

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

Följande C# skriptkoden läser innehållet i den angivna tabellen och returnerar dem till användaren:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel-indata - JavaScript-exempel

Följande *function.json* filen definierar en HTTP-utlösare med en Excel-indatabindning:

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

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribut.

### <a name="excel-input---configuration"></a>Excel-indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Excel` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för Excel-tabellen. Se [med hjälp av en Excel-tabell inkommande bindningen från kod](#excel-input-code).|
|**typ**||Krävs – måste vara inställd på `excel`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**Sökväg**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|**WorksheetName**|Kalkylbladet där tabellen finns.|
|**tableName**|**tableName**|Namnet på tabellen. Om inget anges används innehållet i kalkylbladet.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-indata - användning

Den här bindningen kräver följande behörigheter i Azure AD:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läs användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- String []]
- Microsoft.Graph.WorkbookTable
- Anpassade objektgrupper (med strukturella modellbindning)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-utdata

Excel utdata bindning ändrar innehållet i en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-output---example)
* [Attribut](#excel-output---attributes)
* [Konfiguration](#excel-output---configuration)
* [Användning](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-utdata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel utdata - exempel på C#-skript

I följande exempel lägger till rader till en Excel-tabell.

Den *function.json* filen definierar en HTTP-utlösare med en Excel-utdatabindning:

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

C#-skriptkod lägger till en ny rad i tabellen (antas vara en kolumn) baserat på indata från frågesträngen:

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

#### <a name="excel-output---javascript-example"></a>Excel utdata - JavaScript-exempel

I följande exempel lägger till rader till en Excel-tabell.

Den *function.json* filen definierar en HTTP-utlösare med en Excel-utdatabindning:

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

Följande JavaScript-kod lägger till en ny rad i tabellen (antas vara en kolumn) baserat på indata från frågesträngen.

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

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Excel` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för auth-token. Se [med hjälp av en Excel-tabell utdatabindning från kod](#excel-output-code).|
|**typ**||Krävs – måste vara inställd på `excel`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID** |**Användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**Sökväg**|**Sökväg**|Obligatoriskt - sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|**WorksheetName**|Kalkylbladet där tabellen finns.|
|**tableName**|**tableName**|Namnet på tabellen. Om inget anges används innehållet i kalkylbladet.|
|**Uppdateringstyp**|**Uppdateringstyp**|Obligatoriskt - typ av ändring som du gör i tabellen. Kan vara något av följande värden:<ul><li><code>update</code> -Ersätter innehållet i tabellen i OneDrive.</li><li><code>append</code> -Lägger till nyttolasten i slutet av tabellen i OneDrive genom att skapa nya rader.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel utdata - användning

Den här bindningen kräver följande behörigheter i Azure AD:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- String []]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Anpassade objektgrupper (med strukturella modellbindning)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Filen indata

OneDrive filen indatabindning läser innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-input---example)
* [Attribut](#file-input---attributes)
* [Konfiguration](#file-input---configuration)
* [Användning](#file-input---usage)

### <a name="file-input---example"></a>Filen indata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Indata - filen C# exempel på skript

I följande exempel läser en fil som lagras i OneDrive.

Den *function.json* filen definierar en HTTP-utlösare med indatabindning för filen som ska användas som OneDrive:

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

C#-skriptkod läser filen som anges i frågesträngen och loggar längden:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Indata - filen JavaScript-exempel

I följande exempel läser en fil som lagras i OneDrive.

Den *function.json* filen definierar en HTTP-utlösare med indatabindning för filen som ska användas som OneDrive:

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

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `OneDrive` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i funktionskod för filen. Se [med hjälp av en fil på OneDrive inkommande bindningen från kod](#onedrive-input-code).|
|**typ**||Krävs – måste vara inställd på `onedrive`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**Sökväg**|**Sökväg**|Krävs – sökvägen till filen i OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Filen indata - användning

Den här bindningen kräver följande behörigheter i Azure AD:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läs användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- byte
- Stream
- sträng
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Utdata

Filen OneDrive utdata bindning ändrar innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-output---example)
* [Attribut](#file-output---attributes)
* [Konfiguration](#file-output---configuration)
* [Användning](#file-output---usage)

### <a name="file-output---example"></a>Filen utdata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Filen utdata - exempel på C#-skript

I följande exempel skriver till en fil som lagras i OneDrive.

Den *function.json* filen definierar en HTTP-utlösare med en OneDrive utdatabindning:

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

C#-skriptkod hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt som definierats i föregående exempel) i roten på anroparens OneDrive:

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

#### <a name="file-output---javascript-example"></a>Output - filen JavaScript-exempel

I följande exempel skriver till en fil som lagras i OneDrive.

Den *function.json* filen definierar en HTTP-utlösare med en OneDrive utdatabindning:

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

JavaScript-kod hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt enligt definitionen i konfigurationen ovan) i roten på anroparens OneDrive.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Filen utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribut.

### <a name="file-output---configuration"></a>Filen utdata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `OneDrive` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i funktionskod för filen. Se [med hjälp av en fil på OneDrive-utdatabindning från kod](#onedrive-output-code).|
|**typ**||Krävs – måste vara inställd på `onedrive`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID** |**Användar-ID** |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**Sökväg**|**Sökväg**|Krävs – sökvägen till filen i OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Filen utdata - användning

Den här bindningen kräver följande behörigheter i Azure AD:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen visar följande typer till .NET-funktioner:
- byte
- Stream
- sträng
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-utdata

Outlook-meddelande utdata bindning skickar ett e-postmeddelande via Outlook.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#outlook-output---example)
* [Attribut](#outlook-output---attributes)
* [Konfiguration](#outlook-output---configuration)
* [Användning](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Utdata för Outlook - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook utdata - exempel på C#-skript

I följande exempel skickar ett e-postmeddelande via Outlook.

Den *function.json* filen definierar en HTTP-utlösare med ett Outlook meddelandet utdatabindning:

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

C#-skriptkod skickar ett e-postmeddelande från anroparen till en mottagare som angetts i frågesträngen:

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

#### <a name="outlook-output---javascript-example"></a>Outlook utdata - JavaScript-exempel

I följande exempel skickar ett e-postmeddelande via Outlook.

Den *function.json* filen definierar en HTTP-utlösare med ett Outlook meddelandet utdatabindning:

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

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Outlook` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `outlook`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook utdata - användning

Den här bindningen kräver följande behörigheter i Azure AD:
|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Skicka e-post som användare|

Bindningen visar följande typer till .NET-funktioner:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- sträng
- Anpassade objektgrupper (med strukturella modellbindning)






## <a name="webhooks"></a>Webhook-konfigurationer

Webhooks kan du ta hänsyn till händelser i Microsoft Graph. För att stödja webhooks, funktioner som krävs för att skapa, uppdatera och ta hänsyn till _webhook prenumerationer_. En fullständig webhook-lösning kräver en kombination av bindningarna som följande:
- En [Microsoft Graph webhook utlösaren](#webhook-trigger) kan du ta hänsyn till en inkommande webhook.
- En [Microsoft Graph webhook prenumeration inkommande bindningen](#webhook-input) kan du visa en lista med befintliga prenumerationer och du kan också uppdatera dem.
- En [Microsoft Graph webhook prenumeration utdatabindning](#webhook-output) gör att du kan skapa eller ta bort webhooken prenumerationer.

Bindningar själva kräver inte några Azure AD-behörigheter, men du måste begära behörigheter som är relevanta för den resurstyp som du vill ta hänsyn till. En lista som behövs för varje resurstyp av finns [Prenumerationsbehörighet](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Läs mer om webhooks [arbeta med webhooks i Microsoft Graph].





## <a name="webhook-trigger"></a>Webhook-utlösare

Microsoft Graph webhook-utlösare gör en funktion att reagera på en inkommande webhook från Microsoft Graph. Varje instans av den här utlösaren kan reagera på en resurstyp för Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-trigger---example)
* [Attribut](#webhook-trigger---attributes)
* [Konfiguration](#webhook-trigger---configuration)
* [Användning](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-utlösare - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook - utlösare i C# exempel på skript

I följande exempel hanterar webhooks för inkommande meddelanden i Outlook. För att använda en webhook utlösa du [skapa en prenumeration](#webhook-output---example), och du kan [uppdatera prenumerationen](#webhook-subscription-refresh) att förhindra att den upphör.

Den *function.json* filen definierar en webhook-utlösare:

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

C#-skriptkod reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och som innehåller ”Azure Functions” i ämnesraden:

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook - utlösare i JavaScript-exempel

I följande exempel hanterar webhooks för inkommande meddelanden i Outlook. För att använda en webhook utlösa du [skapa en prenumeration](#webhook-output---example), och du kan [uppdatera prenumerationen](#webhook-subscription-refresh) att förhindra att den upphör.

Den *function.json* filen definierar en webhook-utlösare:

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

JavaScript-kod reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och som innehåller ”Azure Functions” i ämnesraden:

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

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) attribut.

### <a name="webhook-trigger---configuration"></a>Webhook-utlösare - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `GraphWebHookTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `graphWebhook`.|
|**riktning**||Krävs – måste vara inställd på `trigger`.|
|**resourceType**|**resourceType**|Obligatoriskt - diagram-resurs som den här funktionen ska svara på webhooks. Kan vara något av följande värden:<ul><li><code>#Microsoft.Graph.Message</code> -ändringar som gjorts i Outlook-meddelanden.</li><li><code>#Microsoft.Graph.DriveItem</code> -ändrade OneDrive rot-objekt.</li><li><code>#Microsoft.Graph.Contact</code> -ändringar som gjorts i personliga kontakter i Outlook.</li><li><code>#Microsoft.Graph.Event</code> -ändringar som gjorts i kalenderobjekt i Outlook.</li></ul>|

> [!Note]
> En funktionsapp kan bara ha en funktion som har registrerats mot en given `resourceType` värde.

### <a name="webhook-trigger---usage"></a>Webhook-utlösare - användning

Bindningen visar följande typer till .NET-funktioner:
- Microsoft Graph SDK typer som är relevanta för resurstyp, `Microsoft.Graph.Message` eller `Microsoft.Graph.DriveItem`.
- Anpassade objektgrupper (med strukturella modellbindning)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook-indata

Microsoft Graph webhook-indatabindning kan du hämta listan över prenumerationer som hanteras av den här funktionen appen. Bindningen läser från funktionen app lagring, så att den inte återspeglar andra prenumerationer som skapats från utanför appen.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-input---example)
* [Attribut](#webhook-input---attributes)
* [Konfiguration](#webhook-input---configuration)
* [Användning](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook-indata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook-indata - exempel på C#-skript

I följande exempel hämtar alla prenumerationer för den anropande användaren och tar bort dem.

Den *function.json* filen definierar en HTTP-utlösare med en prenumeration indatabindning och en prenumeration utdata bindning som använder åtgärden ta bort:

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

C#-skriptkod hämtar prenumerationerna och tar bort dem:

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

#### <a name="webhook-input---javascript-example"></a>Webhook-indata - JavaScript-exempel

I följande exempel hämtar alla prenumerationer för den anropande användaren och tar bort dem.

Den *function.json* filen definierar en HTTP-utlösare med en prenumeration indatabindning och en prenumeration utdata bindning som använder åtgärden ta bort:

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

JavaScript-kod hämtar prenumerationerna och tar bort dem:

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

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribut.

### <a name="webhook-input---configuration"></a>Webhook-indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `GraphWebHookSubscription` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `graphWebhookSubscription`.|
|**riktning**||Krävs – måste vara inställd på `in`.|
|**Filter**|**Filter**| Om värdet `userFromRequest`, och sedan bindningen endast hämtar prenumerationer som ägs av den anropande användaren (bara giltigt med [HTTP-utlösare]).| 

### <a name="webhook-input---usage"></a>Webhook-indata - användning

Bindningen visar följande typer till .NET-funktioner:
- String]
- Anpassade objekt typen matriser
- Newtonsoft.Json.Linq.JObject]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook-utdata

Webhook-prenumerationen utdata bindning kan du skapa, ta bort och uppdatera webhook prenumerationer i Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-output---example)
* [Attribut](#webhook-output---attributes)
* [Konfiguration](#webhook-output---configuration)
* [Användning](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook-utdata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook utdata - exempel på C#-skript

I följande exempel skapas en prenumeration. Du kan [uppdatera prenumerationen](#webhook-subscription-refresh) att förhindra att den upphör.

Den *function.json* filen definierar en HTTP-utlösare med en prenumeration utdata bindning med åtgärden Skapa:

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

C#-skriptkod registrerar en webhook som meddelar den här funktionen appen när den anropande användaren tar emot ett Outlook-meddelande:

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

#### <a name="webhook-output---javascript-example"></a>Webhook utdata - JavaScript-exempel

I följande exempel skapas en prenumeration. Du kan [uppdatera prenumerationen](#webhook-subscription-refresh) att förhindra att den upphör.

Den *function.json* filen definierar en HTTP-utlösare med en prenumeration utdata bindning med åtgärden Skapa:

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

JavaScript-koden registrerar en webhook som meddelar den här funktionen appen när den anropande användaren tar emot ett Outlook-meddelande:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribut.

### <a name="webhook-output---configuration"></a>Webhook-utdata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `GraphWebHookSubscription` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**||Obligatoriskt - variabelnamnet som används i Funktionskoden för e-postmeddelandet. Se [med ett Outlook-meddelande utdatabindning från kod](#outlook-output-code).|
|**typ**||Krävs – måste vara inställd på `graphWebhookSubscription`.|
|**riktning**||Krävs – måste vara inställd på `out`.|
|**Identitet**|**Identitet**|Obligatoriskt - identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [HTTP-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> -Använder identiteten för en tidigare användare med angivet ID. Finns det <code>userId</code> egenskapen.</li><li><code>userFromToken</code> -Använder den identitet som representeras av den angivna token. Finns det <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> -Använder identiteten för funktionen appen.</li></ul>|
|**Användar-ID**|**Användar-ID**  |Krävs om och bara om _identitet_ är inställd på `userFromId`. En användare ägar-ID som är associerade med en tidigare användare.|
|**userToken**|**UserToken**|Krävs om och bara om _identitet_ är inställd på `userFromToken`. En token är giltig för funktionen appen. |
|**Åtgärden**|**Åtgärd**|Obligatoriskt – anger åtgärden bindningen ska utföra. Kan vara något av följande värden:<ul><li><code>create</code> -Registrerar en ny prenumeration.</li><li><code>delete</code> -Tar bort en angiven prenumeration.</li><li><code>refresh</code> – Uppdaterar en angiven prenumeration om du vill behålla upphör att gälla.</li></ul>|
|**SubscriptionResource**|**SubscriptionResource**|Krävs om och bara om den _åtgärd_ är inställd på `create`. Anger Microsoft Graph-resurs som ska övervakas för ändringar. Se [arbeta med webhooks i Microsoft Graph]. |
|**ChangeType**|**ChangeType**|Krävs om och bara om den _åtgärd_ är inställd på `create`. Anger vilken typ av ändring i den prenumererar på resurs som ska generera ett meddelande. Värdena som stöds är: `created`, `updated`, `deleted`. Du kan kombinera flera värden med hjälp av en kommaavgränsad lista.|

### <a name="webhook-output---usage"></a>Webhook utdata - användning

Bindningen visar följande typer till .NET-funktioner:
- sträng
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook-prenumeration uppdatering

Det finns två sätt att uppdatera prenumerationer:

- Använd programidentiteten för att hantera alla prenumerationer. Detta kräver godkännande från en Azure Active Directory-administratör. Detta kan användas av alla språk som stöds av Azure Functions.
- Använda det identitet som är associerade med varje prenumeration genom att manuellt binda varje användar-ID. Detta kräver vissa anpassad kod för att utföra bindningen. Detta kan endast användas av .NET-funktioner.

Det här avsnittet innehåller ett exempel för var och en av dessa metoder:

* [Exempel för App-identitet](#webhook-subscription-refresh---app-identity-example)
* [Användarens identitet exempel](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook-prenumeration uppdatera - app identity-exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Uppdatera identitet App - exempel på C#-skript

I följande exempel används programidentiteten för att uppdatera en prenumeration.

Den *function.json* definierar en timer som utlösare med en prenumeration indatabindning och en prenumeration utdatabindning:

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

C#-skriptkod uppdaterar prenumerationerna:

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

### <a name="app-identity-refresh---c-script-example"></a>Uppdatera identitet App - exempel på C#-skript

I följande exempel används programidentiteten för att uppdatera en prenumeration.

Den *function.json* definierar en timer som utlösare med en prenumeration indatabindning och en prenumeration utdatabindning:

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

JavaScript-kod uppdaterar prenumerationerna:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Uppdatera Webhook prenumeration - användarens identitet exempel

I följande exempel används användar-ID för att uppdatera en prenumeration.

Den *function.json* filen definierar en timer som utlösare och skjuts upp prenumerationen inkommande bindningen till Funktionskoden:

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

C#-skriptkod uppdaterar prenumerationerna och skapar utdata bindningen i kod, med varje användar-ID:

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

[HTTP-utlösare]: functions-bindings-http-webhook.md
[arbeta med webhooks i Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
