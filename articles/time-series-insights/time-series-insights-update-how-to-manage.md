---
title: Etablera och hantera för hands versionen av Azure Time Series | Microsoft Docs
description: Förstå hur du etablerar och hanterar Azure Time Series Insights för hands version.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47c96cb14f2e466d02d57f73fb2e66bc1f44a311
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989910"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Etablera och hantera Azure Time Series Insights för hands version

Den här artikeln beskriver hur du skapar och hanterar en Azure Time Series Insights Preview-miljö med hjälp av [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Översikt

Azure Time Series Insights Preview-miljöer är PAYG-miljöer (betalar per användning).

När du etablerar en Azure Time Series Insights för hands versions miljö skapar du följande Azure-resurser:

* En Azure Time Series Insights för hands versions miljö  
* Ett Azure Storage allmänt v1-konto
* En valfri varm lagring för snabbare och obegränsade frågor
  
Lär dig [hur du planerar din miljö](./time-series-insights-update-plan.md).

Associera varje Azure Time Series Insights för hands versions miljö med en händelse källa. Mer information finns i [lägga till en händelse nav källa](./time-series-insights-how-to-add-an-event-source-eventhub.md) och [lägga till en källa för IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). Du anger en egenskap för timestamp-ID och en unik konsument grupp under det här steget. Detta säkerställer att miljön har åtkomst till lämpliga händelser.

> [!NOTE]
> Föregående steg är valfritt i etablerings arbets flödet när du skapar tids serie förhands gransknings miljön. Du måste dock koppla en händelse källa till miljön så att data kan börja flöda i den miljön.

När etableringen är klar kan du ändra dina åtkomst principer och andra miljöattribut så att de passar dina affärs behov.

## <a name="create-the-environment"></a>Skapa miljön

Så här skapar du en Azure Time Series Insights för hands versions miljö:

1. På **SKU** -menyn väljer du knappen **PAYG** . Ange ett miljö namn och välj den prenumerations grupp och resurs grupp som du vill använda. Välj sedan en plats som stöds för att vara värd för miljön.

   [![skapa en Azure Time Series Insights instans.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Ange ett Time Series-ID.

    >[!NOTE]
    > * Time Series-ID: t är Skift läges känsligt och oföränderligt. (Den kan inte ändras efter att den har angetts.)
    > * Time Series-ID: n kan vara upp till tre nycklar.
    > * Mer information om hur du väljer ett Time Series-ID finns i [Välj ett Time Series-ID](./time-series-insights-update-how-to-id.md).

1. Skapa ett Azure Storage-konto genom att välja ett lagrings konto namn och ange ett alternativ för replikering. Om du gör det skapas automatiskt ett Azure Storage allmänna v1-konto. Kontot skapas i samma region som Azure Time Series Insights för hands versions miljön som du har valt tidigare.

    [![skapa ett Azure Storage-konto för din instans](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Valfritt)** Aktivera varmt lagrings utrymme för din miljö om du vill ha snabbare och obegränsade frågor över de senaste data i din miljö. Du kan också skapa eller ta bort ett varmt lager via alternativet **lagrings konfiguration** i det vänstra navigerings fönstret när du har skapat en Time Series Insights för hands version.

1. **(Valfritt)** Du kan lägga till en händelse källa nu. Du kan också vänta tills instansen har etablerats.

   * Time Series Insights stöder [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) och [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) som alternativ för händelse källan. Även om du bara kan lägga till en enskild händelse källa när du skapar miljön, kan du lägga till en annan händelse källa senare. Du kan välja en befintlig konsument grupp eller skapa en ny konsument grupp när du lägger till händelse källan. Det är bäst att skapa en unik konsument grupp för att se till att alla händelser är synliga för din Azure Time Series Insights Preview-miljö.

   * Välj lämplig timestamp-egenskap. Som standard använder Azure Time Series Insights den meddelande-köade tiden för varje händelse källa.

     > [!TIP]
     > Den meddelande-som är i kö kanske inte är den bästa konfigurerade inställningen som används i scenarier med batch-händelser eller historiska data överförings scenarier. I sådana fall måste du kontrol lera att du har angett att du vill använda eller inte använder en timestamp-egenskap.

     [![fliken händelse källa](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Bekräfta att din miljö har etablerats med de inställningar du vill ha.

    [![Granska + fliken Skapa](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din Azure Time Series Insights Preview-miljö med hjälp av Azure Portal. När du hanterar via Azure Portal kan du se några viktiga skillnader mellan en förhands gransknings miljö som Azure Time Series Insights du betalar per användning och de allmänt tillgängliga S1-eller S2-miljöerna:

* Bladet **Översikt över** Azure Portal är oförändrat i Azure Time Series Insights, förutom på följande sätt:
  * Kapaciteten tas bort eftersom den inte gäller för "betala per användning"-miljöer.
  * Egenskapen Time Series ID har lagts till. Den avgör hur dina data partitioneras.
  * Referens data uppsättningar tas bort.
  * Den URL som visas leder till [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
  * Namnet på ditt Azure Storage-konto visas.

* Bladet **konfigurera** Azure Portal är borttaget i Azure Time Series Insights för hands versionen eftersom PAYG-miljöer inte kan konfigureras. Du kan dock använda **Storage-konfiguration** för att konfigurera den nyligen lanserade butiken.

* Bladet **referens data** för Azure Portal tas bort i Azure Time Series Insights för hands versionen eftersom referens data inte ingår i "betala per användning"-miljöer.

[![Time Series Insights för hands versions miljö i Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Time Series Insights allmänt tillgängliga miljöer och för hands versioner genom [att läsa planera din miljö](./time-series-insights-update-plan.md).

- Lär dig hur du [lägger till en Event Hub-källa](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurera en [IoT Hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md).
