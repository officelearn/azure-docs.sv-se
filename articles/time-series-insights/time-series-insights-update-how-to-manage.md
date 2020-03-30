---
title: Etablera och hantera en förhandsversionsmiljö – Azure Time Series | Microsoft-dokument
description: Lär dig hur du etablerar och hanterar en förhandsversion av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087212"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Paketerar och hanterar förhandsversionen av Azure Time Series Insights

I den här artikeln beskrivs hur du skapar och hanterar en förhandsversion av Azure Time Series Insights med hjälp av [Azure-portalen](https://portal.azure.com/).

## <a name="overview"></a>Översikt

Förhandsversionsmiljöer för Azure Time Series Insights är *PAYG-miljöer (pay-as-you-go).*

När du etablerar en förhandsversion av Azure Time Series Insights skapar du dessa Azure-resurser:

* Förhandsversionsmiljö för Azure Time Series Insights  
* Ett azure storage-konto för allmänt syfte
* En varm butik som tillval för snabbare och obegränsade frågor

> [!TIP]
> * Läs om hur du [planerar din miljö](./time-series-insights-update-plan.md).
> * Läs om hur [du lägger till en händelsehubbkälla](./time-series-insights-how-to-add-an-event-source-eventhub.md) eller hur [du lägger till en IoT-hubbkälla](./time-series-insights-how-to-add-an-event-source-iothub.md).

Du lär dig att göra följande:

1. **(Valfritt)** Associera varje Azure Time Series Insights Preview-miljö med en händelsekälla. Du kommer också att tillhandahålla en tidsstämpel-ID-egenskap och en unik konsumentgrupp för att säkerställa att miljön har tillgång till lämpliga händelser.

   > [!NOTE]
   > Föregående steg är valfritt när du etablerar en miljö. Om du hoppar över det här steget måste du ansluta en händelsekälla till miljön senare så att data kan nås i miljön.

1. När etableringen är klar kan du ändra dina åtkomstprinciper och andra miljöattribut så att de passar dina affärsbehov.

## <a name="create-the-environment"></a>Skapa miljön

Så här skapar du en förhandsversion av Azure Time Series Insights:

1. Välj **PAYG** som **nivå**. Ange ett miljönamn och välj den prenumerationsgrupp och resursgrupp som ska användas. Välj sedan en plats som stöds för miljön.

   [![Skapa en Azure Time Series Insights-instans.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Ange ett tidsserie-ID.

    > [!NOTE]
    > * Tidsserie-ID:n är *skiftlägeskänslig* och *oföränderlig*. (Det går inte att ändra när den har ställts in.)
    > * Tidsserie-ID:n kan vara upp till *tre* tangenter.
    > * Läs mer om [Hur du väljer ett tidsserie-ID](time-series-insights-update-how-to-id.md)

1. Skapa ett Azure Storage-konto genom att välja ett lagringskontonamn och ange ett replikeringsval. Om du gör det skapas automatiskt ett Azure Storage-v1-konto för allmänt syfte. Kontot skapas i samma region som förhandsversionen av Azure Time Series Insights som du tidigare har valt.

    [![Konfiguration av kall lagring](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Valfritt)** Aktivera varmlagring för din miljö om du vill ha snabbare och obegränsade frågor via de senaste data i din miljö. Du kan också skapa eller ta bort ett varmt arkiv via alternativet **Lagringskonfiguration** i det vänstra navigeringsfönstret när du har skapat en förhandsgranskningsmiljö för timeseriestatistik.

    [![Konfiguration för varm lagring](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Valfritt)** Du kan lägga till en händelsekälla nu. Du kan också vänta tills instansen har etablerats.

   * Time Series Insights stöder [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) och [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) som alternativ för händelsekälla. Även om du bara kan lägga till en enskild händelsekälla när du skapar miljön kan du lägga till en annan händelsekälla senare. 
   
     Du kan välja en befintlig konsumentgrupp eller skapa en ny konsumentgrupp när du lägger till händelsekällan. Det är bäst att skapa en unik konsumentgrupp för att säkerställa att alla händelser är synliga för din Azure Time Series Insights Preview-miljö.

   * Välj lämplig tidsstämpelegenskap. Som standard använder Azure Time Series Insights den meddelandeundventa tiden för varje händelsekälla.

     > [!TIP]
     > Den meddelandeundventid kanske inte är den bästa konfigurerade inställningen att använda i batchhändelsescenarier eller historiska scenarier för dataöverföring. I sådana fall måste du kontrollera ditt beslut att använda eller inte använda en tidsstämpelegenskap.

     [![Konfigurationsfliken För händelsekälla](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Bekräfta att din miljö har etablerats och konfigurerats som du vill.

    [![Fliken Granska + Skapa](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din Förhandsversion av Azure Time Series Insights med hjälp av Azure-portalen. Det finns några viktiga skillnader mellan en PAYG Azure Time Series Insights Preview-miljö och de allmänt tillgängliga S1- eller S2-miljöerna att tänka på när du hanterar din miljö via Azure-portalen:

* Bladet översikt över **Azure** Portal Preview har följande ändringar:

  * Kapaciteten tas bort eftersom den inte gäller för PAYG-miljöer.
  * Egenskapen **Tidsserie-ID** läggs till. Den avgör hur dina data är partitionerade.
  * Referensdatauppsättningar tas bort.
  * Den visade URL:en leder dig till [utforskaren för förhandsversionen](./time-series-insights-update-explorer.md)av Azure Time Series Insights .
  * Ditt Azure Storage-kontonamn visas.

* Azure-portalens **konfigurera** blad tas bort i förhandsversionen av Azure Time Series Insights eftersom PAYG-miljöer inte kan konfigureras. Du kan dock använda **lagringskonfiguration** för att konfigurera det nyligen introducerade varma arkivet.

* Azure-portalens **referensdatablad** tas bort i förhandsversionen av Azure Time Series Insights eftersom referensdata inte ingår i PAYG-miljöer.

[![Förhandsversion av Time Series Insights-miljö i Azure-portalen](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Time Series Insights allmänt tillgängliga miljöer och förhandsgranskningsmiljöer genom att läsa [Planera din miljö](./time-series-insights-update-plan.md).

- Läs om hur du [lägger till en händelsehubbkälla](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurera en [IoT-hubbkälla](./time-series-insights-how-to-add-an-event-source-iothub.md).
