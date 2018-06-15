---
title: Den externa filen bindningar för Azure Functions (försök)
description: Med den externa filen bindningar i Azure Functions
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
ms.locfileid: "27607929"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions extern fil Bindningar (försök)
Den här artikeln visar hur du hanterar filer från olika SaaS-providrar (till exempel Dropbox eller Google Drive) i Azure Functions. Azure Functions stöder utlösa indata och utdata bindningar för de externa filerna. Dessa bindningar skapa API-anslutningar till SaaS-providrar, eller Använd befintliga API-anslutningar från appen funktionen resursgrupp.

> [!IMPORTANT]
> Den externa filen bindningar är experiment och kanske aldrig når vanligtvis tillgänglighet (GA) status. De är inkluderad i Azure Functions 1.x och det finns inga planer på att lägga till dem i Azure Functions 2.x. Överväg att använda för scenarier som kräver åtkomst till data i SaaS-providers [logikappar som anropar funktioner](functions-twitter-email.md). Finns det [Logic Apps filsystemet connector](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Tillgängliga anslutningar

|Anslutning|Utlösare|Indata|Resultat|
|:-----|:---:|:---:|:---:|
|[Rutan](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive för företag](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google-enhet](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Den externa filen anslutningar kan också användas i [Azure Logikappar](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Utlösare

Den externa fil utlösaren kan du övervaka en fjärransluten mapp och köra Funktionskoden när ändringar identifieras.

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#-skript](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en extern fil utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar innehållet i varje fil som har lagts till i mappen övervakade.

Här är de bindande data den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Här är skriptkod C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en extern fil utlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen loggar innehållet i varje fil som har lagts till i mappen övervakade.

Här är de bindande data den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Egenskapen Function.JSON | Beskrivning|
|---------|---------|----------------------|
|**typ** | måste anges till `apiHubFileTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar händelsen i funktionskoden. | 
|**anslutning**| Identifierar appinställningen som lagrar anslutningssträngen. Appinställningen skapas automatiskt när du lägger till en anslutning i integrera Användargränssnittet i Azure-portalen.|
|**sökväg** | Mappen för att övervaka och eventuellt ett mönster.|

### <a name="name-patterns"></a>Namnet mönster

Du kan ange ett filnamnsmönster i den `path` egenskapen. Den mapp som refererar till måste finnas i SaaS-providern.

Exempel:

```json
"path": "input/original-{name}",
```

Den här sökvägen hittade en fil med namnet *ursprungliga File1.txt* i den *inkommande* mapp och värdet för den `name` variabel i Funktionskoden skulle vara `File1.txt`.

Ett annat exempel:

```json
"path": "input/{filename}.{fileextension}",
```

Den här sökvägen kan också söka efter en fil med namnet *ursprungliga File1.txt*, och värdet för den `filename` och `fileextension` variabler i Funktionskoden skulle vara *ursprungliga File1* och *txt*.

Du kan begränsa filtypen för filer med hjälp av ett fast värde för filnamnstillägget. Exempel:

```json
"path": "samples/{name}.png",
```

I det här fallet bara *.png* filer i den *exempel* mappen utlösa funktionen.

Klammerparenteser är specialtecken i namnet mönster. Om du vill ange filnamn som innehåller klammerparenteser i namnet dubbla klammerparenteser.
Exempel:

```json
"path": "images/{{20140101}}-{name}",
```

Den här sökvägen hittade en fil med namnet *{20140101}-soundfile.mp3* i den *bilder* mapp, och `name` variabelvärdet i Funktionskoden skulle vara *soundfile.mp3*.

## <a name="trigger---usage"></a>Utlösaren - användning

I C#-funktioner, du binder till filen med indata-data med hjälp av en namngiven parameter i en funktionssignaturen som `<T> <name>`.
Där `T` är datatypen som du vill att deserialisera data till och `paramName` är det namn du angav i den [utlösa JSON](#trigger). I Node.js-funktion, du åtkomst till filen med indata-data med `context.bindings.<name>`.

Filen kan avserialiseras till någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialiserad fildata.
  Om du deklarerar en anpassad Indatatyp (t.ex. `FooType`), Azure Functions försöker att deserialisera JSON-data till den angivna typen.
* String - användbart för data från text.

Du kan också binda till någon av följande typer i C#-funktioner, och Functions-runtime försöker att deserialisera filen data med hjälp av den typen:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>Utlösaren - skadligt filer

När en extern fil Utlösarfunktion inte återförsök Azure Functions funktionen upp till 5 gånger som standard (inklusive första försöket) för en viss fil.
Om alla 5 försök misslyckas funktioner läggs ett meddelande till en kö för lagring med namnet *webjobs-apihubtrigger-poison*. Kömeddelande för skadligt filer är en JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet  *&lt;funktionen appnamn >*. Funktioner.  *&lt;funktionsnamn >*)
* Filtyp
* Mappnamn
* Filnamn
* ETag (en fil versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

## <a name="input"></a>Indata

Azure extern fil indatabindning kan du använda en fil från en extern mapp i din funktion.

## <a name="input---example"></a>Indata - exempel

Finns i det språkspecifika:

* [C#-skript](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

I följande exempel visas den externa filen inkommande och utgående bindningar i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen kopierar en indatafil till en utdatafil.

Här är de bindande data den *function.json* fil:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är skriptkod C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Indata - JavaScript-exempel

I följande exempel visas den externa filen inkommande och utgående bindningar i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen kopierar en indatafil till en utdatafil.

Här är de bindande data den *function.json* fil:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-kod:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Egenskapen Function.JSON | Beskrivning|
|---------|---------|----------------------|
|**typ** | måste anges till `apiHubFile`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar händelsen i funktionskoden. | 
|**anslutning**| Identifierar appinställningen som lagrar anslutningssträngen. Appinställningen skapas automatiskt när du lägger till en anslutning i integrera Användargränssnittet i Azure-portalen.|
|**sökväg** | Måste innehålla namnet på mappen och filnamnet. Om du har till exempel en [kö utlösaren](functions-bindings-storage-queue.md) i din funktion kan du använda `"path": "samples-workitems/{queueTrigger}"` att peka till en fil i den `samples-workitems` mapp med ett namn som matchar filnamnet som anges i meddelandet för utlösare.   

## <a name="input---usage"></a>Indata - användning

I C#-funktioner, du binder till filen med indata-data med hjälp av en namngiven parameter i en funktionssignaturen som `<T> <name>`. `T`är de datatyp som du vill att deserialisera data till och `name` är det namn du angav i den inkommande bindningen. I Node.js-funktion, du åtkomst till filen med indata-data med `context.bindings.<name>`.

Filen kan avserialiseras till någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialiserad fildata.
  Om du deklarerar en anpassad Indatatyp (t.ex. `InputType`), Azure Functions försöker att deserialisera JSON-data till den angivna typen.
* String - användbart för data från text.

Du kan också binda till någon av följande typer i C#-funktioner, och Functions-runtime försöker att deserialisera filen data med hjälp av den typen:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Resultat

Den externa filen Azure utdata bindning kan du skriva filer till en extern mapp i din funktion.

## <a name="output---example"></a>Output - exempel

Finns det [indatabindning exempel](#input---example).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Egenskapen Function.JSON | Beskrivning|
|---------|---------|----------------------|
|**typ** | måste anges till `apiHubFile`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar händelsen i funktionskoden. | 
|**anslutning**| Identifierar appinställningen som lagrar anslutningssträngen. Appinställningen skapas automatiskt när du lägger till en anslutning i integrera Användargränssnittet i Azure-portalen.|
|**sökväg** | Måste innehålla namnet på mappen och filnamnet. Om du har till exempel en [kö utlösaren](functions-bindings-storage-queue.md) i din funktion kan du använda `"path": "samples-workitems/{queueTrigger}"` att peka till en fil i den `samples-workitems` mapp med ett namn som matchar filnamnet som anges i meddelandet för utlösare.   

## <a name="output---usage"></a>Utdata - användning

I C#-funktioner, du binder till utdatafilen med hjälp av den namngivna `out` parameter i en funktionssignaturen som `out <T> <name>`, där `T` är datatypen som du vill serialisera data till och `name` är det namn du angav i utdata-bindning. I Node.js-funktion, du åtkomst till filen utdata med `context.bindings.<name>`.

Du kan skriva till utdatafilen med någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialisering.
  Om du deklarerar en anpassad utdatatypen (t.ex. `out OutputType paramName`), Azure Functions-försök att serialisera objektet till JSON. Om output-parameter är null när funktionen avslutas skapas Functions-runtime en fil som ett null-objekt.
* String - (`out string paramName`) användbart för data från text. Functions-runtime skapas en fil endast om strängparametern är icke-null när funktionen avslutas.

I C#-funktioner kan du även spara till någon av följande typer:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
