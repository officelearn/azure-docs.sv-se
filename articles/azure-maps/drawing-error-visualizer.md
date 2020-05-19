---
title: Använda Azure Maps ritnings fel visualiserare
description: 'I den här artikeln får du lära dig mer om hur du visualiserar varningar och fel som returneras av skapare konverterings-API: et.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e1b5b16d5522285f2d028303f3295cc1fb740330
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598817"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>Använda Azure Maps ritnings fel visualiserare

Ritnings fel Visualiseraren är ett fristående webb program som visar [varningar från ritnings paket och fel som](drawing-conversion-error-codes.md) upptäckts under konverterings processen. Webb programmet för fel visualiserare består av en statisk sida som du kan använda utan att ansluta till Internet.  Du kan använda fel Visualiseraren för att åtgärda fel och varningar i enlighet med [kraven för ritnings paket](drawing-requirements.md). [Azure Maps Conversion API](https://docs.microsoft.com/rest/api/maps/data/conversion) returnerar bara ett svar med en länk till fel visualiseraren endast när ett fel upptäcks.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan ladda ned ritnings fel Visualiseraren måste du:

1. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. [Skapa en skapare resurs](how-to-manage-creator.md)

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="download"></a>Ladda ned

1. Ladda upp ditt Drawing-paket till tjänsten Azure Maps Creator för att hämta ett `udid` för det överförda paketet. Anvisningar om hur du laddar upp ett paket finns i [Ladda upp ett ritnings paket](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Nu när ritnings paketet har överförts använder vi `udid` för det överförda paketet för att konvertera paketet till kartdata. Anvisningar om hur du konverterar ett paket finns i [konvertera ett ritnings paket](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Om konverterings processen lyckas får du inte någon länk till fel visualisera-verktyget.

3. Under fliken svars **rubriker** i Postman-programmet, letar du efter `diagnosticPackageLocation` egenskapen som returnerades av konverterings-API: et. Svaret bör se ut som följande JSON:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Hämta fel Visualiseraren för ritnings paket genom att göra en `HTTP-GET` begäran på URL: en `diagnosticPackageLocation` .

## <a name="setup"></a>Installation

I det hämtade zippade paketet från `diagnosticPackageLocation` länken hittar du två filer.

* _VisualizationTool. zip_: innehåller käll koden, mediet och webb sidan för ritnings fel visualiseraren.
* _ConversionWarningsAndErrors. JSON_: innehåller en formaterad lista med varningar, fel och ytterligare information som används av fel visualiseraren för ritning.

Zippa upp mappen _VisualizationTool. zip_ . Den innehåller följande objekt:

* mappen _till gångar_ : innehåller bilder och mediefiler
* _statisk_ mapp: käll kod
* _index. html-_ fil: webb programmet.

Öppna filen _index. html_ med någon av Webbläsarena nedan, med respektive versions nummer. Du kan använda en annan version, om versionen har ett likvärdigt kompatibelt beteende som den listade versionen.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Använda verktyget Drawing Error visualiserare

När du har startat verktyget Rita fel visualiserare visas sidan uppladdning. Överförings sidan innehåller en dra och släpp-ruta. List rutan dra & fungerar också som en knapp som öppnar en dialog ruta i Utforskaren.

![App för att rita fel visualiserare – start sida](./media/drawing-errors-visualizer/start-page.png)

Filen _ConversionWarningsAndErrors. JSON_ har placerats i roten för den hämtade katalogen. Om du vill läsa in _ConversionWarningsAndErrors. JSON_ kan du antingen dra & släpper filen i rutan eller klicka på rutan, leta upp filen i dialog rutan för Utforskaren och sedan ladda upp filen.

![Att rita fel visualisera appar – dra och släpp för att läsa in data](./media/drawing-errors-visualizer/loading-data.gif)

När _ConversionWarningsAndErrors. JSON_ -filen har lästs in visas en lista över fel och varningar i ritnings paketet. Varje fel eller varning anges av lagret, nivån och ett detaljerat meddelande. Du kan nu navigera till varje fel för att lära dig mer om hur du löser problemet.  

![Drawing Error-program-fel och varningar](./media/drawing-errors-visualizer/errors.png)

## <a name="next-steps"></a>Nästa steg

När ditt [Draw-paket uppfyller kraven](drawing-requirements.md)kan du använda [tjänsten Azure Maps data uppsättning](https://docs.microsoft.com/rest/api/maps/data/conversion) för att konvertera ritnings paketet till en data uppsättning. Sedan kan du använda webb modulen inomhus Maps för att utveckla ditt program. Läs mer i följande artiklar:

> [!div class="nextstepaction"]
> [Fel koder för ritnings konvertering](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Skapare för inomhus Maps](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Använda modulen inomhus Maps](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementera dynamisk formatering i inomhus-karta](indoor-map-dynamic-styling.md)