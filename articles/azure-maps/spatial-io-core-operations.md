---
title: Centrala I/o-verksamhet | Microsoft Azure Maps
description: Lär dig hur du effektivt läser och skriver XML och avgränsade data med hjälp av kärnbibliotek från den rumsliga IO-modulen.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371445"
---
# <a name="core-io-operations"></a>Kärn-I/O-verksamhet

Förutom att tillhandahålla verktyg för att läsa rumsliga datafiler, exponerar den rumsliga IO-modulen grundläggande underliggande bibliotek för att läsa och skriva XML och avgränsade data snabbt och effektivt.

Namnområdet `atlas.io.core` innehåller två lågnivåklasser som snabbt kan läsa och skriva CSV- och XML-data. Dessa basklasser driver rumsliga dataläsare och författare i den rumsliga IO-modulen. Använd dem gärna för att lägga till ytterligare stöd för läsning och skrivning för CSV- eller XML-filer.
 
## <a name="read-delimited-files"></a>Läsa avgränsade filer

Klassen `atlas.io.core.CsvReader` läser strängar som innehåller avgränsade datauppsättningar. Den här klassen innehåller två metoder för att läsa data:

- Funktionen `read` kommer att läsa hela datauppsättningen och returnera en tvådimensionell matris med strängar som representerar alla celler i den avgränsade datauppsättningen.
- Funktionen `getNextRow` läser varje textrad i en avgränsad datauppsättning och returnerar en matris med sträng som representerar alla celler i den datauppsättningen. Användaren kan bearbeta raden och ta bort eventuellt onödigt minne från den raden innan nästa rad bearbetas. Så, funktion är mer minneseffektiv.

Som standard använder läsaren kommatecknet som avgränsare. Avgränsare kan dock ändras till ett enda `'auto'`tecken eller ställas in på . När den `'auto'`är inställd på kommer läsaren att analysera den första textraden i strängen. Sedan kommer det att välja det vanligaste tecknet i tabellen nedan för att använda som avgränsare.

| | |
| :-- | :-- |
| Komma | `,` |
| Flik | `\t` |
| Pipe | `|` |

Den här läsaren stöder också textkomvigering som används för att hantera celler som innehåller tecknet avgränsare. Citattecknet`'"'`( ) är standardtextkvalificeraren, men det kan ändras till ett enda tecken.

## <a name="write-delimited-files"></a>Skriva avgränsade filer

Skriver `atlas.io.core.CsvWriter` en matris med objekt som en avgränsad sträng. Ett enda tecken kan användas som avgränsare eller textkvalificerare. Standardavgränsaren är komma`','`( ) och standardtextkvalificeraren`'"'`är citattecknet ( ).

Så här använder du den här klassen nedan:

- Skapa en förekomst av klassen och ange eventuellt en anpassad avgränsare eller textkvalificerare.
- Skriv data till klassen `write` med `writeRow` hjälp av funktionen eller funktionen. För `write` funktionen skickar du en tvådimensionell matris med objekt som representerar flera rader och celler. Om du `writeRow` vill använda funktionen skickar du en matris med objekt som representerar en rad med data med flera kolumner.
- Anropa `toString` funktionen för att hämta den avgränsade strängen. 
- Du kan också `clear` anropa metoden för att göra författaren återanvändbar och `delete` minska resursallokeringen, eller anropa metoden för att göra sig av med writer-instansen.

> [!Note]
> Antalet skrivna kolumner begränsas till antalet celler i den första raden i data som skickas till författaren.

## <a name="read-xml-files"></a>Läsa XML-filer

Klassen `atlas.io.core.SimpleXmlReader` är snabbare när XML-filer `DOMParser`analyseras än . `atlas.io.core.SimpleXmlReader` Klassen kräver dock att XML-filer är väl formaterade. XML-filer som inte är väl formaterade, till exempel saknade stängningstaggar, kommer sannolikt att resultera i ett fel.

Följande kod visar hur du `SimpleXmlReader` använder klassen för att tolka en XML-sträng till ett JSON-objekt och serialisera den till önskat format.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Skriva XML-filer

Klassen `atlas.io.core.SimpleXmlWriter` skriver välformaterad XML på ett minneseffektivt sätt.

Följande kod visar hur du `SimpleXmlWriter` använder klassen för att generera en välformaterad XML-sträng.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Den genererade XML-koden från ovanstående kod skulle se ut så här.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [CsvReader (0)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter (0)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter (1000/00](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)