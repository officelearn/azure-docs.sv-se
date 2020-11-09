---
title: Lägga till paneler till din Azure IoT Central-instrumentpanel | Microsoft Docs
description: Som ett verktyg kan du läsa om hur du konfigurerar standard instrument panelen för Azure IoT Central-program med paneler.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5276f9b8c6dd3bdc305142e5b9452cd9c5d60bce
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94377004"
---
# <a name="configure-the-application-dashboard"></a>Konfigurera program instrument panelen

**Instrument panelen** är den första sidan som visas när du ansluter till ett IoT Central-program. Om du skapar ditt program från en av de branschspecifika [Programmallarna](./concepts-app-templates.md)har ditt program en fördefinierad instrument panel att starta. Om du skapar ditt program från en anpassad [Programmall](./concepts-app-templates.md), visar instrument panelen några tips för att komma igång.

> [!TIP]
> Användare kan [skapa flera instrument paneler](howto-create-personal-dashboards.md) förutom standard instrument panelen. Dessa instrument paneler kan endast vara personliga för användaren eller delas över alla användare av programmet.  

## <a name="add-tiles"></a>Lägg till paneler

På följande skärm bild visas instrument panelen i ett program som skapats från den **anpassade program** mal len. Om du vill anpassa den aktuella instrument panelen väljer du **Redigera** , för att lägga till en anpassad personlig eller delad instrument panel, väljer **ny** :

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Instrument panel för program som baseras på den anpassade program mal len":::

När du har valt **redigera** eller **nytt** är instrument panelen i *redigerings* läge. Du kan använda verktygen på panelen **Redigera instrument panel** för att lägga till paneler på instrument panelen och anpassa och ta bort paneler på själva instrument panelen. Om du till exempel vill lägga till en **telemetri** -panel för att visa aktuell temperatur som rapporteras av en eller flera enheter:

1. I panelen **Redigera instrument panel** väljer du en **enhets grupp**.
1. Välj en eller flera enheter i list rutan **enheter** som ska visas på panelen. Nu ser du tillgängliga telemetri, egenskaper och kommandon från enheterna.
1. Välj **temperatur** i avsnittet telemetri och välj sedan **Lägg till panel**. Panelen visas nu på instrument panelen där du kan ändra visualiseringen, ändra storlek på panelen och konfigurera den:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Lägg till en panel för temperatur telemetri på instrument panelen":::

När du är klar med att lägga till och anpassa paneler på instrument panelen väljer du **Spara**.

## <a name="customize-tiles"></a>Anpassa paneler

Om du vill anpassa en panel på instrument panelen måste instrument panelen vara i redigerings läge. De tillgängliga anpassnings alternativen beror på [panel typen](#tile-types):

* Med ikonen linjal på en panel kan du ändra visualiseringen. Visualiseringar innehåller linje diagram, senaste kända värden och värme kartor.

* Med ikonen fyrkant kan du ändra storlek på panelen.

* Med kugg hjuls ikonen kan du konfigurera visualiseringen. För en linje diagram visualisering kan du till exempel välja att Visa förklaringen och axlarna och välja tidsintervallet som ska ritas.

## <a name="tile-types"></a>Panel typer

I följande tabell beskrivs de olika typerna av panel som du kan lägga till på en instrument panel:

| Panel             | Description |
| ---------------- | ----------- |
| Markdown         | Markdown paneler är klicknings bara paneler som visar en rubrik och en beskrivnings text formaterad med markdown. URL: en kan vara en relativ länk till en annan sida i programmet eller en absolut länk till en extern plats.|
| Bild            | Bild paneler visar en anpassad bild och går att klicka på. URL: en kan vara en relativ länk till en annan sida i programmet eller en absolut länk till en extern plats.|
| Etikett            | Etikett paneler visar anpassad text på en instrument panel. Du kan välja storlek på texten. Använd en etikett panel för att lägga till relevant information på instrument panelen, till exempel beskrivningar, kontakt uppgifter eller hjälp.|
| Antal            | Antal paneler visar antalet enheter i en enhets grupp.|
| Karta              | Kart paneler visar platsen för en eller flera enheter på en karta. Du kan också visa upp till 100 punkter i enhetens plats historik. Du kan till exempel Visa en exempel väg om var en enhet har varit den senaste veckan.|
| KPI              |  KPI-paneler visar sammanlagda värden för telemetri för en eller flera enheter under en tids period. Du kan till exempel använda den för att visa den maximala temperatur och belastning som har nåtts för en eller flera enheter under den senaste timmen.|
| Linjediagram       | Linje diagram paneler ritar ett eller flera sammanställda telemetri värden för en eller flera enheter under en tids period. Du kan till exempel visa ett linje diagram för att rita upp genomsnitts temperaturen och trycket för en eller flera enheter under den senaste timmen.|
| Stapeldiagram        | Liggande diagram paneler ritar ett eller flera sammanställda telemetridata för en eller flera enheter under en tids period. Du kan till exempel visa ett stapeldiagram för att Visa genomsnitts temperaturen och trycket för en eller flera enheter under den senaste timmen.|
| Cirkeldiagram        | Cirkel diagram paneler visar en eller flera sammanställda telemetri värden för en eller flera enheter under en tids period.|
| Termisk karta         | I panelerna för termisk karta visas information om en eller flera enheter som visas som färger.|
| Senast kända värde | Sista kända värde panelerna visar de senaste telemetri-värdena för en eller flera enheter. Du kan till exempel använda den här panelen för att visa de senaste temperatur-, tryck-och fuktighets värdena för en eller flera enheter. |
| Händelse historik    | Panelerna händelse historik visar händelserna för en enhet under en viss tids period. Du kan till exempel använda den för att visa alla ventiler öppna och Stäng händelser för en eller flera enheter under den senaste timmen.|
| Egenskap         |  Egenskaps paneler visar det aktuella värdet för egenskaper och moln egenskaper för en eller flera enheter. Du kan till exempel använda den här panelen för att Visa enhets egenskaper som tillverkare eller inbyggd program vara för en enhet. |

För närvarande kan du lägga till upp till 10 enheter till paneler som har stöd för flera enheter.

### <a name="customizing-visualizations"></a>Anpassa visualiseringar

Linje diagram visar som standard data under ett tidsintervall. Det valda tidsintervallet delas upp i 50-enheter med samma storlek och enhets data sammanställs sedan per Bucket för att ge 50 data punkter över det valda tidsintervallet. Om du vill visa rå data kan du ändra ditt val för att visa de senaste 100 värdena. Om du vill ändra tidsintervallet eller välja rå data visualisering, använder du List rutan visnings intervall i panelen **Konfigurera diagram** .

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Ändra visnings intervallet för ett linje diagram":::

För paneler som visar aggregerade värden väljer du kugg hjuls ikonen bredvid typen telemetri i panelen **Konfigurera diagram** för att välja agg regeringen. Du kan välja mellan genomsnitt, sum, Max, minimum och Count.

För linje diagram, liggande diagram och cirkel diagram kan du anpassa färgen på de olika värdena för telemetri. Välj palett-ikonen bredvid den telemetri som du vill anpassa:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Ändra färg på ett telemetri värde":::

För paneler som visar sträng egenskaper eller telemetridata kan du välja hur du vill att texten ska visas. Om enheten till exempel lagrar en URL i en sträng egenskap kan du Visa den som en klicknings bara länk. Om URL: en refererar till en bild kan du återge bilden i ett sista känt värde eller en egenskaps panel. Om du vill ändra hur en sträng visas i panelen konfiguration väljer du kugg hjuls ikonen bredvid typen av telemetri eller egenskap:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Ändra hur en sträng visas på en panel":::

För numeriska **KPI** : er, **senast kända värde** och **egenskaps** paneler kan du använda villkorsstyrd formatering för att anpassa panelens färg baserat på dess aktuella värde. Om du vill lägga till villkorsstyrd formatering väljer du **Konfigurera** på panelen och väljer sedan ikonen **villkorsstyrd formatering** bredvid värdet som du vill anpassa:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Skärm bild som visar hur du hittar alternativet Konfigurera för en panel och sedan ikonen villkorsstyrd formatering":::

Lägg till reglerna för villkorsstyrd formatering:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Skärm bild som visar regler för villkorsstyrd formatering för Genomsnittligt flöde. Det finns tre regler som är mindre Tha 20 är gröna, mindre än 50 är gult och allt över 50 är rött":::

Följande skärm bild visar resultatet av regeln för villkorsstyrd formatering:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Skärm bild som visar den gula bakgrunds färgen på panelen Genomsnittligt vatten flöde. Numret på panelen är 40,84":::

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din Azure IoT Central standard instrument panel, kan du [lära dig hur du skapar en personlig instrument panel](howto-create-personal-dashboards.md).
