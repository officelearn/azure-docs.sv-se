---
title: Etablera och hantera en gen 2-miljö – Azure Time Series | Microsoft Docs
description: Lär dig hur du etablerar och hanterar en Azure Time Series Insights gen 2-miljö.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 7c38c57a8480ef2addde494b94d70bd2eb679373
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016776"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Etablera och hantera Azure Time Series Insights Gen2

Den här artikeln beskriver hur du skapar och hanterar en Azure Time Series Insights Gen2-miljö med hjälp av [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Översikt

När du etablerar en Azure Time Series Insights Gen2-miljö skapar du följande Azure-resurser:

* En Azure Time Series Insights Gen2-miljö som följer pris modellen betala per användning
* Ett Azure Storage konto
* En valfri varm lagring för snabbare och obegränsade frågor

> [!TIP]
>
> * Lär dig [hur du planerar din miljö](./how-to-plan-your-environment.md).
> * Läs om hur du [lägger till en Event Hub-källa](./how-to-ingest-data-event-hub.md) eller hur du [lägger till en källa för IoT Hub](./how-to-ingest-data-iot-hub.md).

Du lär dig hur du:

1. Koppla varje Azure Time Series Insights gen 2-miljö till en händelse källa. Du kommer också att ange en egenskap för timestamp-ID och en unik konsument grupp för att säkerställa att miljön har åtkomst till lämpliga händelser.

1. När etableringen är klar kan du ändra dina åtkomst principer och andra miljöattribut så att de passar dina affärs behov.

   > [!NOTE]
   > Det första steget är valfritt när du konfigurerar en miljö. Om du hoppar över det här steget måste du koppla en händelse källa till miljön senare så att data kan börja flöda till din miljö och kan nås via frågan.

## <a name="create-the-environment"></a>Skapa miljön

Så här skapar du en Azure Time Series Insights gen 2-miljö:

1. Skapa en Azure Time Series Insights resurs under *Sakernas Internet* på [Azure Portal](https://portal.azure.com/).

1. Välj **Gen2 (L1)** som **nivå**. Ange ett miljö namn och välj den prenumerations grupp och resurs grupp som du vill använda. Välj sedan en plats som stöds för att vara värd för miljön.

   [![Skapa en Azure Time Series Insights-instans.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Ange ett Time Series-ID.

    > [!NOTE]
    >
    > * Time Series-ID: t är Skift läges *känsligt* och *oföränderligt*. (Den kan inte ändras efter att den har angetts.)
    > * Time Series-ID: n kan vara upp till *tre* nycklar. Tänk på det som en primär nyckel i en databas som unikt representerar varje enhets sensor som skickar data till din miljö. Det kan vara en egenskap eller en kombination av upp till tre egenskaper.
    > * Läs mer om [hur du väljer ett Time Series-ID](./how-to-select-tsid.md)

1. Skapa ett Azure Storage konto genom att välja ett lagrings konto namn, en konto typ och utse ett [replikeringsalternativ](../storage/common/redundancy-migration.md?tabs=portal) . Om du gör det skapas ett Azure Storage-konto automatiskt. Som standard skapas [generell användning v2](../storage/common/storage-account-overview.md) -konto. Kontot skapas i samma region som den Azure Time Series Insights Gen2-miljö som du har valt tidigare.
Du kan också ta med din egen lagring (BYOS) genom [arm-mallen](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) när du skapar en ny Azure Time Series Gen2-miljö.

1. **(Valfritt)** Aktivera varmt lagrings utrymme för din miljö om du vill ha snabbare och obegränsade frågor över de senaste data i din miljö. Du kan också skapa eller ta bort ett varmt lager via alternativet **lagrings konfiguration** i det vänstra navigerings fönstret när du har skapat en Azure Time Series Insights Gen2-miljö.

1. **(Valfritt)** Du kan lägga till en händelse källa nu. Du kan också vänta tills instansen har etablerats.

   * Azure Time Series Insights stöder [azure IoT Hub](./how-to-ingest-data-iot-hub.md) och [Azure Event Hubs](./how-to-ingest-data-event-hub.md) som alternativ för händelse källan. Även om du bara kan lägga till en enskild händelse källa när du skapar miljön, kan du lägga till en annan händelse källa senare.

     Du kan välja en befintlig konsument grupp eller skapa en ny konsument grupp när du lägger till händelse källan. Observera att händelse källan kräver en unik konsument grupp för att din miljö ska kunna läsa data i den.

   * Välj lämplig timestamp-egenskap. Som standard använder Azure Time Series Insights den meddelande-köade tiden för varje händelse källa.

     > [!TIP]
     > Den meddelande-som är i kö kanske inte är den bästa konfigurerade inställningen som används i scenarier med batch-händelser eller historiska data överförings scenarier. I sådana fall måste du kontrol lera att du har angett att du vill använda eller inte använder en timestamp-egenskap.

     [![Fliken Konfiguration av händelse källa](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Bekräfta att din miljö har etablerats och kon figurer ATS på det sätt som du vill.

    [![Granska + fliken Skapa](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din Azure Time Series Insights Gen2-miljö med hjälp av Azure Portal. Det finns några viktiga skillnader mellan en Gen2 miljö och gen1 S1-eller gen1 S2-miljöer som du bör tänka på när du hanterar din miljö via Azure Portal:

* Bladet Azure Portal Gen2- **Översikt**  har följande ändringar:

  * Kapaciteten tas bort eftersom den inte gäller för Gen2-miljöer.
  * Egenskapen **Time Series ID** har lagts till. Den avgör hur dina data partitioneras.
  * Referens data uppsättningar tas bort.
  * Den URL som visas leder till [Azure Time Series Insights Explorer](./concepts-ux-panels.md).
  * Namnet på Azure Storages kontot visas.

* Bladet **konfigurera** Azure Portal är borttaget eftersom skalnings enheter inte gäller för Azure Time Series Insights Gen2-miljöer. Du kan dock använda **Storage-konfiguration** för att konfigurera den nyligen lanserade butiken.

* Bladet **referens data** för Azure Portal tas bort i Azure Time Series Insights Gen2 eftersom referens data koncept har ersatts av [Time Series-modellen (TSM)](./concepts-model-overview.md).

[![Azure Time Series Insights Gen2-miljö i Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Time Series Insights allmänt tillgängliga miljöer och Gen2-miljöer genom att läsa [Planera din miljö](./how-to-plan-your-environment.md).

* Lär dig hur du [lägger till en Event Hub-källa](./how-to-ingest-data-event-hub.md).

* Konfigurera en [IoT Hub-källa](./how-to-ingest-data-iot-hub.md).