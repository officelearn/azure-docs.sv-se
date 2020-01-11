---
title: Etablera och hantera en för hands versions miljö – Azure Time Series | Microsoft Docs
description: Lär dig hur du etablerar och hanterar en Azure Time Series Insights för hands versions miljö.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: b70604c62ae21f9c433b3cd7d9e59f4ccebb61bd
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861735"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Etablera och hantera Azure Time Series Insights för hands version

Den här artikeln beskriver hur du skapar och hanterar en Azure Time Series Insights Preview-miljö med hjälp av [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Översikt

Azure Time Series Insights Preview-miljöer är PAYG-miljöer ( *betalar per* användning).

När du etablerar en Azure Time Series Insights för hands versions miljö skapar du följande Azure-resurser:

* En Azure Time Series Insights för hands versions miljö  
* Ett Azure Storage allmänt v1-konto
* En valfri varm lagring för snabbare och obegränsade frågor

> [!TIP]
> * Lär dig [hur du planerar din miljö](./time-series-insights-update-plan.md).
> * Läs om hur du [lägger till en Event Hub-källa](./time-series-insights-how-to-add-an-event-source-eventhub.md) eller hur du [lägger till en källa för IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

Du lär dig att göra följande:

1. **(Valfritt)** Associera varje Azure Time Series Insights för hands versions miljö med en händelse källa. Du kommer också att ange en egenskap för timestamp-ID och en unik konsument grupp för att säkerställa att miljön har åtkomst till lämpliga händelser.

   > [!NOTE]
   > Föregående steg är valfritt när du konfigurerar en miljö. Om du hoppar över det här steget måste du koppla en händelse källa till miljön senare så att det går att komma åt data i miljön.

1. När etableringen är klar kan du ändra dina åtkomst principer och andra miljöattribut så att de passar dina affärs behov.

## <a name="create-the-environment"></a>Skapa miljön

Så här skapar du en Azure Time Series Insights för hands versions miljö:

1. Välj **PAYG** som **nivå**. Ange ett miljö namn och välj den prenumerations grupp och resurs grupp som du vill använda. Välj sedan en plats som stöds för att vara värd för miljön.

   [![skapa en Azure Time Series Insights instans.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Ange ett Time Series-ID.

    > [!NOTE]
    > * Time Series-ID: t är Skift läges *känsligt* och *oföränderligt*. (Den kan inte ändras efter att den har angetts.)
    > * Time Series-ID: n kan vara upp till *tre* nycklar.
    > * Läs mer om [hur du väljer ett Time Series-ID](time-series-insights-update-how-to-id.md)

1. Skapa ett Azure Storage konto genom att välja ett lagrings konto namn och ange ett alternativ för replikering. Om du gör det skapas automatiskt ett Azure Storage allmänna v1-konto. Kontot skapas i samma region som Azure Time Series Insights för hands versions miljön som du har valt tidigare.

    [konfiguration av ![kall lagring](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Valfritt)** Aktivera varmt lagrings utrymme för din miljö om du vill ha snabbare och obegränsade frågor över de senaste data i din miljö. Du kan också skapa eller ta bort ett varmt lager via alternativet **lagrings konfiguration** i det vänstra navigerings fönstret när du har skapat en Time Series Insights för hands version.

    [![varmt lagrings konfiguration](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Valfritt)** Du kan lägga till en händelse källa nu. Du kan också vänta tills instansen har etablerats.

   * Time Series Insights stöder [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) och [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) som alternativ för händelse källan. Även om du bara kan lägga till en enskild händelse källa när du skapar miljön, kan du lägga till en annan händelse källa senare. 
   
     Du kan välja en befintlig konsument grupp eller skapa en ny konsument grupp när du lägger till händelse källan. Det är bäst att skapa en unik konsument grupp för att se till att alla händelser är synliga för din Azure Time Series Insights Preview-miljö.

   * Välj lämplig timestamp-egenskap. Som standard använder Azure Time Series Insights den meddelande-köade tiden för varje händelse källa.

     > [!TIP]
     > Den meddelande-som är i kö kanske inte är den bästa konfigurerade inställningen som används i scenarier med batch-händelser eller historiska data överförings scenarier. I sådana fall måste du kontrol lera att du har angett att du vill använda eller inte använder en timestamp-egenskap.

     [![fliken Konfiguration av händelse källa](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Bekräfta att din miljö har etablerats och kon figurer ATS på det sätt som du vill.

    [![Granska + fliken Skapa](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din Azure Time Series Insights Preview-miljö med hjälp av Azure Portal. Det finns några viktiga skillnader mellan en PAYG Azure Time Series Insights för hands version och de allmänt tillgängliga S1-eller S2-miljöerna som du bör tänka på när du hanterar din miljö via Azure Portal:

* Bladet Azure Portal för för hands versions **Översikt** har följande ändringar:

  * Kapaciteten tas bort eftersom den inte gäller för PAYG-miljöer.
  * Egenskapen **Time Series ID** har lagts till. Den avgör hur dina data partitioneras.
  * Referens data uppsättningar tas bort.
  * Den URL som visas leder till [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
  * Namnet på Azure Storages kontot visas.

* Bladet **konfigurera** Azure Portal är borttaget i Azure Time Series Insights för hands versionen eftersom PAYG-miljöer inte kan konfigureras. Du kan dock använda **Storage-konfiguration** för att konfigurera den nyligen lanserade butiken.

* Bladet **referens data** för Azure Portal tas bort i Azure Time Series Insights för hands versionen eftersom referens data inte ingår i PAYG-miljöer.

[![Time Series Insights för hands versions miljö i Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Time Series Insights allmänt tillgängliga miljöer och för hands versioner genom [att läsa planera din miljö](./time-series-insights-update-plan.md).

- Lär dig hur du [lägger till en Event Hub-källa](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurera en [IoT Hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md).
