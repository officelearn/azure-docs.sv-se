---
title: Azure Time Series Preview-hantering – hur du etablerar och hanterar Azure Time Series Preview. | Microsoft Docs
description: För att förstå hur du etablerar och hanterar Azure Time Series Insights Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273525"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Hur du etablerar och hanterar Azure Time Series Insights Preview

Det här dokumentet beskriver hur du skapar och hanterar en förhandsversion av Azure Time Series Insights-miljö med hjälp av den [Azure-portalen](https://portal.azure.com/).

## <a name="overview"></a>Översikt

En kort översikt över hur du etablerar en förhandsversion av Azure Time Series Insights-miljö beskrivs nedan:

* Azure Time Series Insights Preview-miljöer är **PAYG** miljöer.
  * Som en del av processen måste du ange en Time Series-ID. Time Series-ID: N kan vara upp till tre nycklar. Läs mer om hur du väljer en tid-ID genom att läsa [hur du väljer en tid-ID](./time-series-insights-update-how-to-id.md).
  * När du etablerar en förhandsversion av Azure Time Series Insights-miljö kan skapa du två Azure-resurser:

    * En förhandsversion av Azure Time Series Insights-miljö  
    * Ett Azure Storage-allmänna V1-konto
  
    Lär dig [hur du planerar din miljö](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > För kunder som använder V2-konton, aktivera inte kall/arkivering egenskaper för lagringskontot som du kommer att använda.

* Varje förhandsversion av Azure Time Series Insights-miljö kan kopplas (valfritt) en händelsekälla. Läs [hur du lägger till en Event Hub-källa](./time-series-insights-how-to-add-an-event-source-eventhub.md) och [hur du lägger till en IoT Hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md) för mer information.
  * Du får en tidsstämpel-ID-egenskap och en unik konsumentgrupp under det här steget. På så sätt kan att miljön har åtkomst till lämplig händelserna.

* När etableringen är klar, kan du ändra dina affärskrav till dina principer för åtkomst och andra miljö-attribut till suite.

## <a name="new-environment-creation"></a>Skapa en ny miljö

Följande steg beskriver hur du skapar en förhandsversion av Azure Time Series Insights-miljö:

1. Välj den **PAYG** knappen den **SKU** menyn. Ange ett miljönamn på, ange vilken prenumerationsgrupp och vilken resursgrupp som du använder. Välj en plats som stöds för den miljö som ska köras i.

1. Ange en tid-ID

    >[!NOTE]
    > * Time Series-ID är skiftlägeskänsliga och kan ändras (det kan inte ändras efter att det angetts).
    > * Time Series-ID: N kan vara upp till 3 nycklar.
    > * Läs mer om att välja en Time-ID genom att läsa [hur du väljer en tid-ID](./time-series-insights-update-how-to-id.md).

1. Skapa ett Azure Storage-konto genom att välja ett Azure Storage-kontonamn och ange ett alternativ för replikering. Gör automatiskt skapar ett Azure Storage-allmänna V1-konto. Det kommer att skapas i samma region som förhandsversion av Azure Time Series Insights-miljö som du valde tidigare.

    ![Skapa en Azure Time Series Insights-instans.][1]

1. Alternativt kan du lägga till en händelsekälla.

   * Time Series Insights stöder [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) och [Händelsehubbar](./time-series-insights-how-to-add-an-event-source-eventhub.md) som alternativ. Du kan bara lägga till en enskild händelsekälla vid tidpunkten för skapandet av miljön, kan du lägga till en ytterligare händelsekälla senare. Det är bäst att skapa en unik konsumentgrupp för att säkerställa att alla händelser som är synliga för förhandsversionen av Azure Time Series Insights-instans. Du kan välja en befintlig konsumentgrupp eller skapa en ny konsumentgrupp när du lägger till händelsekällan.

   * Du bör också ange egenskapen tidsstämpel. Som standard använder Azure Time Series Insights Kötid meddelande för varje händelsekälla.

     > [!TIP]
     > Kötid meddelande kanske inte den bästa konfigurerade inställningen för att använda batch händelse eller historiska data överför scenarier. Se till att kontrollera ditt beslut att använda eller inte använda en tidsstämpel-egenskapen i sådana fall.

    ![Lägg till en händelsekälla i din instans.][2]

1. Granska och skapa

    ![Lägg till en händelsekälla i din instans.][3]

Bekräfta att din miljö har etablerats med de önskade inställningarna.

## <a name="management"></a>Hantering

Du har möjlighet att hantera din förhandsversion av Azure Time Series Insights-miljö med Azure portal. Här följer de större skillnaderna i hanteringen av en **PAYG** förhandsversion av Azure Time Series Insights-miljö till skillnad från en S1 eller S2-miljö med hjälp av Azure Portal:

* Azure-portalen *översikt* bladet förblir oförändrad i Azure Time Series Insights utom på följande sätt:
  * Kapacitet tas bort eftersom detta inte är relevanta för **PAYG** miljöer.
  * Time Series-ID-egenskapen har lagts till. Det avgör hur dina data är partitionerad.
  * Referensdatauppsättningar tas bort.
  * Visas URL: en tar dig till den [förhandsversion av Azure Time Series Insights explorer](./time-series-insights-update-explorer.md).
  * Namnet på ditt Azure Storage-konto visas.

* Azure Portal *konfigurera* bladet har tagits bort i förhandsversionen av Azure Time Series Insights eftersom det inte finns **PAYG** miljöer kan inte konfigureras.

* Azure Portal *referens* blad har tagits bort i förhandsversionen av Azure Time Series Insights eftersom referensdata inte är en komponent i **PAYG** miljöer.

![Kontrollera din instans.][4]

## <a name="next-steps"></a>Nästa steg

Läs [hur du planerar din miljö](./time-series-insights-update-plan.md).

Läs [hur du lägger till en Event Hub-källa](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Läs [hur du lägger till en IoT Hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
