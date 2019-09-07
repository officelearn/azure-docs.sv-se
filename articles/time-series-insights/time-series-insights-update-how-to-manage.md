---
title: Etablera och hantera för hands versionen av Azure Time Series | Microsoft Docs
description: Förstå hur du etablerar och hanterar Azure Time Series Insights för hands version.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a84c587a2666982e862f0640b30d2d6bef3b9a42
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744661"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Etablera och hantera Azure Time Series Insights för hands version

Den här artikeln beskriver hur du skapar och hanterar en Azure Time Series Insights Preview-miljö med hjälp av [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Översikt

Azure Time Series Insights Preview-miljöer är PAYG-miljöer (betalar per användning).

När du etablerar en Azure Time Series Insights för hands versions miljö skapar du två Azure-resurser:

* En Azure Time Series Insights för hands versions miljö  
* Ett Azure Storage allmänt v1-konto
  
Lär dig [hur du planerar din miljö](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> För för hands versionen ser du till att du använder ett Azure Storage GPv1-konto (General Purpose v1).

Du kan också associera varje Azure Time Series Insights för hands versions miljö med en händelse källa. Mer information finns i [lägga till en händelse nav källa](./time-series-insights-how-to-add-an-event-source-eventhub.md) och [lägga till en källa för IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). Du anger en egenskap för timestamp-ID och en unik konsument grupp under det här steget. Detta säkerställer att miljön har åtkomst till lämpliga händelser.

När etableringen är klar kan du ändra dina åtkomst principer och andra miljöattribut så att de passar dina affärs behov.

## <a name="create-the-environment"></a>Skapa miljön

Följande steg beskriver hur du skapar en Azure Time Series Insights för hands versions miljö:

1. Välj knappen **PAYG** under **SKU** -menyn. Ange ett miljö namn och välj vilken prenumerations grupp och vilken resurs grupp som ska användas. Välj sedan en plats som stöds för den miljö som ska finnas i.

   [![Skapa en Azure Time Series Insights-instans.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Ange ett Time Series-ID.

    >[!NOTE]
    > * Time Series-ID: t är Skift läges känsligt och oföränderligt. (Den kan inte ändras efter att den har angetts.)
    > * Time Series-ID: n kan vara upp till tre nycklar.
    > * Mer information om hur du väljer ett Time Series-ID finns i [Välj ett Time Series-ID](./time-series-insights-update-how-to-id.md).

1. Skapa ett Azure Storage-konto genom att välja ett lagrings konto namn och ange ett alternativ för replikering. Om du gör det skapas automatiskt ett Azure Storage allmänna v1-konto. Den kommer att skapas i samma region som Azure Time Series Insights för hands versions miljön som du har valt tidigare.

    [![Skapa ett Azure Storage-konto för din instans](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Du kan också lägga till en händelse källa.

   * Time Series Insights stöder [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) och [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) som alternativ. Även om du bara kan lägga till en enskild händelse källa när miljön skapas kan du lägga till en annan händelse källa senare. Det är bäst att skapa en unik konsument grupp för att se till att alla händelser är synliga för din Azure Time Series Insights Preview-instans. Du kan välja en befintlig konsument grupp eller skapa en ny konsument grupp när du lägger till händelse källan.

   * Du bör också välja lämplig timestamp-egenskap. Som standard använder Azure Time Series Insights meddelandets köade tid för varje händelse källa.

     > [!TIP]
     > Meddelandets angivna tid kanske inte är den bästa konfigurerade inställningen för att använda i batch-händelser eller historiska data överförings scenarier. Se till att du verifierar ditt beslut att använda eller inte använder en timestamp-egenskap i sådana fall.

     [![Fliken händelse källa](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Bekräfta att din miljö har etablerats med önskade inställningar.

    [![Granska + fliken Skapa](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din Azure Time Series Insights Preview-miljö med hjälp av Azure Portal. Här är de viktigaste skillnaderna vid hantering av en PAYG Azure Time Series Insights för hands version, i stället för en S1-eller S2-miljö, genom att använda Azure Portal:

* Bladet **Översikt över** Azure Portal är oförändrat i Azure Time Series Insights, förutom på följande sätt:
  * Kapaciteten tas bort eftersom det här konceptet inte är relevant för PAYG-miljöer.
  * Egenskapen Time Series ID har lagts till. Den avgör hur dina data partitioneras.
  * Referens data uppsättningar tas bort.
  * Den URL som visas leder till [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
  * Namnet på ditt Azure Storage-konto visas.

* Bladet för Azure Portal **konfiguration** har tagits bort i Azure Time Series Insights för hands versionen eftersom PAYG-miljöer inte kan konfigureras.

* Bladet **referens data** för Azure Portal har tagits bort i Azure Time Series Insights för hands versionen eftersom referens data inte är en komponent i PAYG-miljöer.

[![Time Series Insights för hands versions miljö i Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs [Planera din miljö](./time-series-insights-update-plan.md).

- Lär dig hur du [lägger till en Event Hub-källa](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurera [en IoT Hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md).