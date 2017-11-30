---
title: "Azure Functions extern fil Bindningar (förhandsversion) | Microsoft Docs"
description: Med den externa filen bindningar i Azure Functions
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: c7a1ff4d4488b37b1969edfbb6f935eccd63413c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Azure Functions extern fil Bindningar (förhandsgranskning)
Den här artikeln visar hur du manipulera filerna från olika SaaS-providrar (t.ex. OneDrive, Dropbox) i din funktion använder inbyggda bindningar. Azure functions stöder utlösa indata och utdata bindningar för extern fil.

Den här bindningen skapar API-anslutningar till SaaS-providrar eller använder befintliga API-anslutningar från appen funktionen resursgrupp.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Stöds filen anslutningar

|Koppling|Utlösare|Indata|Resultat|
|:-----|:---:|:---:|:---:|
|[Rutan](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive för företag](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google-enhet](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Den externa filen anslutningar kan också användas i [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

## <a name="external-file-trigger-binding"></a>Extern fil utlöser bindning

Utlösaren Azure extern fil kan du övervaka en fjärransluten mapp och köra Funktionskoden när ändringar identifieras.

Den externa fil utlösaren använder följande JSON-objekt i den `bindings` matris med function.json

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

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

<a name="receipts"></a>

<!--- ### File receipts
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
<a name="poison"></a>

### <a name="handling-poison-files"></a>Hantering av skadligt filer
När en extern fil Utlösarfunktion inte återförsök Azure Functions funktionen upp till 5 gånger som standard (inklusive första försöket) för en viss fil.
Om alla 5 försök misslyckas funktioner läggs ett meddelande till en kö för lagring med namnet *webjobs-apihubtrigger-poison*. Kömeddelande för skadligt filer är en JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet  *&lt;funktionen appnamn >*. Funktioner.  *&lt;funktionsnamn >*)
* Filtyp
* Mappnamn
* Filnamn
* ETag (en fil versions-ID, till exempel: ”0x8D1DC6E70A277EF”)


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utlösaren användning
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

## <a name="trigger-sample"></a>Utlösaren exempel
Anta att du har följande function.json, som definierar en extern fil-utlösare:

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

Se exemplet språkspecifika loggar innehållet i varje fil som har lagts till i mappen övervakade.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Användning av utlösare i C# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Användning av utlösare i Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Den externa filen inkommande bindningen
Azure extern fil indatabindning kan du använda en fil från en extern mapp i din funktion.

Den externa filen indata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Observera följande:

* `path`måste innehålla namnet på mappen och filnamnet. Om du har till exempel en [kö utlösaren](functions-bindings-storage-queue.md) i din funktion kan du använda `"path": "samples-workitems/{queueTrigger}"` att peka till en fil i den `samples-workitems` mapp med ett namn som matchar filnamnet som anges i meddelandet för utlösare.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Inkommande användning
I C#-funktioner, du binder till filen med indata-data med hjälp av en namngiven parameter i en funktionssignaturen som `<T> <name>`.
Där `T` är datatypen som du vill att deserialisera data till och `paramName` är det namn du angav i den [inkommande bindningen](#input). I Node.js-funktion, du åtkomst till filen med indata-data med `context.bindings.<name>`.

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


<a name="output"></a>

## <a name="external-file-output-binding"></a>Den externa filen utdatabindning
Den externa filen Azure utdata bindning kan du skriva filer till en extern mapp i din funktion.

Den externa filen utdata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Observera följande:

* `path`måste innehålla namnet på mappen och filnamnet för att skriva till. Om du har till exempel en [kö utlösaren](functions-bindings-storage-queue.md) i din funktion kan du använda `"path": "samples-workitems/{queueTrigger}"` att peka till en fil i den `samples-workitems` mapp med ett namn som matchar filnamnet som anges i meddelandet för utlösare.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Användning av utdata
I C#-funktioner, du binder till utdatafilen med hjälp av den namngivna `out` parameter i en funktionssignaturen som `out <T> <name>`, där `T` är datatypen som du vill serialisera data till och `paramName` är det namn du angav i den [utdatabindning](#output). I Node.js-funktion, du åtkomst till filen utdata med `context.bindings.<name>`.

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

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Indata och utdata-exempel
Anta att du har följande function.json som definierar en [lagring kö utlösaren](functions-bindings-storage-queue.md)en extern fil indata och utdata för en extern fil:

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

Se exemplet språkspecifika som kopierar filen till utdatafilen.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Användning i C# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Användning i Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
