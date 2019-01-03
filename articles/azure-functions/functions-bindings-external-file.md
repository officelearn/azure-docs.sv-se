---
title: Den externa filen bindningar för Azure Functions (experimentell)
description: Med bindningar för externa filer i Azure Functions
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: cshoe
ms.openlocfilehash: 765eab8dfc1163c4d9e0337a1af840278ae1a82c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546274"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions extern fil Bindningar (experimentell)
Den här artikeln visar hur man hanterar filer från olika SaaS-leverantörer (till exempel Dropbox eller Google Drive) i Azure Functions. Azure Functions stöder utlösa, indata och utdata-bindningar för externa filer. Dessa bindningar skapa API-anslutningar till SaaS-leverantörer eller Använd befintliga API-anslutningar från resursgruppen för din Funktionsapp.

> [!IMPORTANT]
> Bindningar för externa filer är experimentellt och aldrig kan nå status för allmänt tillgänglig (GA). De ingår endast i Azure Functions 1.x och det finns inga planer på att lägga till dem i Azure Functions 2.x. Överväg att använda för scenarier som kräver åtkomst till data i SaaS-leverantörer, [logikappar som anropar functions](functions-twitter-email.md). Se den [anslutningsappen för filsystem för Logic Apps](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Tillgängliga anslutningar

|Koppling|Utlösare|Indata|Resultat|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive för företag](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Den externa filen anslutningar kan också användas i [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Utlösare

Extern fil-utlösare kan du övervaka en fjärransluten mapp och kör Funktionskoden när ändringar identifieras.

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#-skript](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en extern fil utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar innehållet i varje fil som läggs till i mappen övervakade.

Här är bindningsdata i den *function.json* fil:

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

Här är C#-skriptkoden:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en extern fil utlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen loggar innehållet i varje fil som läggs till i mappen övervakade.

Här är bindningsdata i den *function.json* fil:

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

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Function.JSON egenskap | Beskrivning|
|---------|---------|----------------------|
|**typ** | Måste anges till `apiHubFileTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar objektet händelse i funktionskoden. | 
|**anslutning**| Identifierar den appinställning som lagrar anslutningssträngen. Appinställningen skapas automatiskt när du lägger till en anslutning i integrera UI i Azure-portalen.|
|**Sökväg** | Mappen som ska övervakas och eventuellt en namnmönstret.|

### <a name="name-patterns"></a>Mönster för namn

Du kan ange ett filnamnsmönster i den `path` egenskapen. Den mapp som refererar till måste finnas i SaaS-providern.

Exempel:

```json
"path": "input/original-{name}",
```

Den här sökvägen skulle hitta en fil med namnet *ursprungliga File1.txt* i den *inkommande* mapp och värdet för den `name` variabel i Funktionskoden skulle vara `File1.txt`.

Ett annat exempel:

```json
"path": "input/{filename}.{fileextension}",
```

Den här sökvägen skulle också hitta en fil med namnet *ursprungliga File1.txt*, och värdet för den `filename` och `fileextension` variabler i Funktionskoden skulle vara *ursprungliga fil1* och *txt* .

Du kan begränsa filtypen för filer med hjälp av ett fast värde för filnamnstillägget. Exempel:

```json
"path": "samples/{name}.png",
```

I det här fallet bara *.png* filer i den *exempel* mappen Utlös funktionen.

Klammerparenteser är specialtecken i ett namn-mönster. Om du vill ange filnamn som har klammerparenteser i namnet, dubbelklickar du klammerparenteser.
Exempel:

```json
"path": "images/{{20140101}}-{name}",
```

Den här sökvägen skulle hitta en fil med namnet  *{20140101}-soundfile.mp3* i den *avbildningar* mappen och `name` variabelvärdet i Funktionskoden skulle vara *soundfile.mp3*.

## <a name="trigger---usage"></a>Utlösare - användning

I C#-funktioner, du binder till filen med indata-data med hjälp av en namngiven parameter i din funktionssignatur som `<T> <name>`.
Där `T` är datatypen som du vill att deserialisera data till, och `paramName` är det namn du angav i den [utlösa JSON](#trigger). I Node.js-funktion, du åtkomst till filen med indata-data med `context.bindings.<name>`.

Filen kan avserialiseras till någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialiserat fildata.
  Om du deklarerar en anpassad Indatatyp (t.ex. `FooType`), Azure Functions försöker deserialisera JSON-data till den angivna typen.
* Sträng - användbart för textfildata.

Du kan också binda till någon av följande typer i C#-funktioner, och Functions runtime försöker deserialisera fildata med hjälp av den typen:

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

## <a name="trigger---poison-files"></a>Utlösare – skadliga filer

Om en extern fil Utlösarfunktion misslyckas återförsök Azure Functions som fungerar upp till 5 gånger som standard (inklusive första försöket) för en viss fil.
Om alla 5 försök misslyckas Functions lägger till ett meddelande till en lagringskö med namnet *webjobs-apihubtrigger-poison*. Kömeddelande för skadliga filer är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet  *&lt;funktionsappens namn >*. Funktioner.  *&lt;funktionsnamn >*)
* fileType
* Mappnamn
* Filnamn
* ETag (en fil versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

## <a name="input"></a>Indata

Indatabindningen Azure extern fil kan du använda en fil från en extern mapp i din funktion.

## <a name="input---example"></a>Indata - exempel

Se exempel språkspecifika:

* [C#-skript](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

I följande exempel visas extern fil bindningar för indata och utdata i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen kopierar en indatafil till en utdatafil.

Här är bindningsdata i den *function.json* fil:

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

Här är C#-skriptkoden:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Indata - JavaScript-exempel

I följande exempel visas extern fil bindningar för indata och utdata i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen kopierar en indatafil till en utdatafil.

Här är bindningsdata i den *function.json* fil:

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

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Function.JSON egenskap | Beskrivning|
|---------|---------|----------------------|
|**typ** | Måste anges till `apiHubFile`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar objektet händelse i funktionskoden. | 
|**anslutning**| Identifierar den appinställning som lagrar anslutningssträngen. Appinställningen skapas automatiskt när du lägger till en anslutning i integrera UI i Azure-portalen.|
|**Sökväg** | Måste innehålla namnet på mappen och filnamnet. Exempel: Om du har en [köutlösare](functions-bindings-storage-queue.md) i din funktion kan du använda `"path": "samples-workitems/{queueTrigger}"` så att den pekar till en fil i den `samples-workitems` mapp med ett namn som matchar namnet på filen som anges i meddelandet för utlösaren.   

## <a name="input---usage"></a>Indata - användning

I C#-funktioner, du binder till filen med indata-data med hjälp av en namngiven parameter i din funktionssignatur som `<T> <name>`. `T` är de datatyp som du vill att deserialisera data till, och `name` är det namn du angav i indatabindningen. I Node.js-funktion, du åtkomst till filen med indata-data med `context.bindings.<name>`.

Filen kan avserialiseras till någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialiserat fildata.
  Om du deklarerar en anpassad Indatatyp (t.ex. `InputType`), Azure Functions försöker deserialisera JSON-data till den angivna typen.
* Sträng - användbart för textfildata.

Du kan också binda till någon av följande typer i C#-funktioner, och Functions runtime försöker deserialisera fildata med hjälp av den typen:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Resultat

Azure extern fil utdatabindning kan du skriva filer till en extern mapp i din funktion.

## <a name="output---example"></a>Utdata - exempel

Se den [indatabindning exempel](#input---example).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Function.JSON egenskap | Beskrivning|
|---------|---------|----------------------|
|**typ** | Måste anges till `apiHubFile`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar objektet händelse i funktionskoden. | 
|**anslutning**| Identifierar den appinställning som lagrar anslutningssträngen. Appinställningen skapas automatiskt när du lägger till en anslutning i integrera UI i Azure-portalen.|
|**Sökväg** | Måste innehålla namnet på mappen och filnamnet. Exempel: Om du har en [köutlösare](functions-bindings-storage-queue.md) i din funktion kan du använda `"path": "samples-workitems/{queueTrigger}"` så att den pekar till en fil i den `samples-workitems` mapp med ett namn som matchar namnet på filen som anges i meddelandet för utlösaren.   

## <a name="output---usage"></a>Utdata - användning

I C#-funktioner, du binder till utdatafilen med hjälp av den namngivna `out` parametern i funktionssignaturen, som `out <T> <name>`, där `T` är datatypen som du vill att serialisera data till, och `name` är det namn du angav i utdata-bindning. I Node.js-funktion, du åtkomst till utdatafilen med `context.bindings.<name>`.

Du kan skriva till filen med hjälp av någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialisering.
  Om du deklarerar en anpassad utdatatypen (t.ex. `out OutputType paramName`), Azure Functions-försök att serialisera objekt till JSON. Om output-parameter är null när funktionen avslutas, skapar en fil som ett null-objekt i Functions-körning.
* Sträng - (`out string paramName`) användbart för textfildata. Functions-körning skapar en fil endast om strängparametern är icke-null när funktionen avslutas.

I C#-funktioner kan du också skickas till någon av följande typer:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
