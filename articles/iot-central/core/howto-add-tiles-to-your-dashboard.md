---
title: Lägga till paneler på instrumentpanelen | Microsoft-dokument
description: Som byggare kan du lära dig hur du konfigurerar standardinstrumentpanelen för Azure IoT Central-program.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310288"
---
# <a name="configure-the-application-dashboard"></a>Konfigurera instrumentpanelen för programmet

**Instrumentpanelen** är den sida som läses in när användare som har åtkomst till programmet navigerar till programmets URL. Om du har skapat programmet från en av **programmallarna**har programmet en fördefinierad instrumentpanel att starta. Om du har skapat programmet från programmallen **Äldre** är instrumentpanelen tom för att starta.

> [!NOTE]
> Användare kan [skapa flera instrumentpaneler](howto-create-personal-dashboards.md) utöver standardinstrumentpanelen för program. Dessa instrumentpaneler kan vara personliga för användaren eller delas mellan alla användare av programmet. 

## <a name="add-tiles"></a>Lägg till paneler

Följande skärmbild visar instrumentpanelen i ett program som skapats från mallen **Anpassat program.** Om du vill anpassa standardinstrumentpanelen för ditt program väljer du **Redigera** längst upp till vänster på sidan.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för program som baseras på mallen "Exempelkontos"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Om du väljer **Redigera** öppnas instrumentpanelens bibliotekspanel. Biblioteket innehåller de paneler och instrumentpanelsprimitiver som du kan använda för att anpassa instrumentpanelen.

> [!div class="mx-imgBorder"]
> ![Instrumentpanelsbibliotek](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Du kan till exempel lägga till en **telemetripanel** för enhetens aktuella temperatur. Gör så här:
1. Välj en **enhetsmall**
1. Välj en **enhetsinstans** för den enhet som du vill se på en instrumentpanelspanel. Då ser du en lista över enhetens egenskaper som kan användas på panelen.
1. Om du vill skapa panelen på instrumentpanelen klickar du på **Temperatur** och drar den till instrumentpanelsområdet. Du kan också klicka på kryssrutan bredvid **Temperatur** och klicka på **Kombinera**. Följande skärmbild visar hur du väljer en enhetsmall och enhetsinstans och sedan skapar en temperaturtelemetripanel på instrumentpanelen.
1. Välj **Spara** längst upp till vänster om du vill spara panelen på instrumentpanelen.

> [!div class="mx-imgBorder"]
> ![Formuläret "Konfigurera enhetsinformation" med information om inställningar och egenskaper](media/howto-add-tiles-to-your-dashboard/device-details.png)

Nu när en operatör visar standardprograminstrumentpanelen ser de den nya panelen med **temperaturen** för enheten. Varje panel har ett förvalt diagram, diagram etc. som visas när panelen skapas. Användare kan dock välja att redigera och ändra den här visualiseringen. 

> [!div class="mx-imgBorder"]
> ![Fliken "Instrumentpanel" med visade inställningar och egenskaper för panelen](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Redigera paneler

Om du vill redigera en panel på instrumentpanelen klickar du först på **Redigera** längst upp till vänster på sidan, vilket öppnar redigeringsläget för instrumentpanelen och alla dess paneler. 

> [!div class="mx-imgBorder"]
> ![Instrumentpanelsskärm med redigeringsläge aktiverat för en markerad panel](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Klicka sedan på **ikonen Redskap** i det övre högra hörnet på panelen som du vill redigera. Här kan du redigera aspekter av panelen inklusive dess titel, dess visualisering, aggregering, etc.

> [!div class="mx-imgBorder"]
> ![Listruta för inställningar för panelaggregering](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Du kan också ändra diagramvisualiseringen genom att klicka på **linjalikonen** på panelen.

> [!div class="mx-imgBorder"]
> ![Listruta för panelvisualiseringsinställningar](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Typer av paneler

I följande tabell sammanfattas användningen av paneler i Azure IoT Central:
 
| Panel | Instrumentpanel | Beskrivning
| ----------- | ------- | ------- |
| Innehåll | Instrumentpaneler för program- och enhetsuppsättningar |Paneler som stöds av Markdown är klickbara paneler som visar rubrik och beskrivningstext. Du kan också använda den här panelen som en länkpanel för att göra det möjligt för en användare att navigera till en URL som är relaterad till ditt program.|
| Bild | Instrumentpaneler för program- och enhetsuppsättningar |Bildpaneler visar en anpassad bild och kan vara klickbara. Använd en bildpanel för att lägga till bilder på en instrumentpanel och aktivera en användare så att de navigerar till en URL som är relevant för ditt program.|
| Label (Etikett) | Instrumentpaneler för program |Etikettpaneler visar anpassad text på en instrumentpanel. Du kan välja storleken på texten. Använd en etikettpanel för att lägga till relevant information i instrumentpanelen sådana beskrivningar, kontaktuppgifter eller hjälp.|
| Karta | Instrumentpaneler för program och enheter |Kartpaneler visar platsen för en enhet på en karta. Du kan också visa upp till 100 punkter av en enhets platshistorik. Du kan till exempel visa en visningsursprut om var en enhet har befunnit sig den senaste veckan.|
| Linjediagram | Instrumentpaneler för program och enheter |Paneler med linjediagram visar ett diagram över aggregerad mätning för en enhet under en tidsperiod. Du kan till exempel visa ett linjediagram som visar medeltemperaturen och trycket för en enhet för den senaste timmen.|
| Stapeldiagram | Instrumentpaneler för program och enheter |Stapeldiagrampaneler visar ett diagram över sammanlagda mått för en enhet under en tidsperiod. Du kan till exempel visa ett stapeldiagram som visar medeltemperaturen och trycket för en enhet under den senaste timmen.|
| Cirkeldiagram | Instrumentpaneler för program- och enhetsuppsättningar |Cirkeldiagrampaneler visar ett diagram över aggregerade mått för en enhet under en tidsperiod.|
| Termisk karta | Instrumentpaneler för program- och enhetsuppsättningar |Värmekarta paneler visa information om enhetens uppsättning, representerad som färger.|
| Händelsehistorik | Instrumentpaneler för program och enheter |Paneler för händelsehistorik visar händelserna för en enhet under en tidsperiod. Du kan till exempel använda den för att visa alla temperaturändringar för en enhet under den senaste timmen.|
| Statens historia | Instrumentpaneler för program och enheter |Tillståndshistorikpaneler visar mätvärdena för en tidsperiod. Du kan till exempel använda den för att visa temperaturvärdena för en enhet under den senaste timmen.|
| KPI | Instrumentpaneler för program och enheter | KPI-paneler visar en mängderad telemetri eller händelsemätning under en tidsperiod. Du kan till exempel använda den för att visa den maximala temperaturen som uppnåtts för en enhet under den senaste timmen.|
| Senast kända värde | Instrumentpaneler för program och enheter |Senast kända värdepaneler visar det senaste värdet för en telemetri- eller tillståndsmätning. Du kan till exempel använda den här panelen för att visa de senaste mätningarna av temperatur, tryck och fuktighet för en enhet.|

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar standardinstrumentpanelen för Azure IoT Central-standardprogram kan du [lära dig hur du skapar en personlig instrumentpanel](howto-create-personal-dashboards.md).
