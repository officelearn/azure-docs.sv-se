---
title: Så här övervakar du ett Azure Storage-konto | Microsoft Docs
description: Lär dig hur du övervakar ett lagringskonto i Azure med hjälp av Azure portal.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: ffc7d46bbfa4db47a47e416c395efdfc451cadc1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322921"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Övervaka ett lagringskonto i Azure-portalen

[Azure Storage Analytics](../storage-analytics.md) ger mått för alla lagringstjänster och loggar för BLOB, köer och tabeller. Du kan använda den [Azure-portalen](https://portal.azure.com) konfigurera vilka mått och loggar in för ditt konto och konfigurera diagram som ger visuella representationer av mått-data.

> [!NOTE]
> Det finns kostnader i samband med att undersöka övervakningsdata i Azure-portalen. Mer information finns i [Storage Analytics och fakturering för](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Azure Files för närvarande stöder Storage Analytics mätvärden, men ännu stöder inte loggning.
> 
> En detaljerad vägledning om använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka problem med Azure Storage finns [övervaka, diagnostisera och felsöka Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurera övervakning för ett lagringskonto

1. I den [Azure-portalen](https://portal.azure.com)väljer **lagringskonton**, sedan lagringskontonamnet att öppna instrumentpanelen konto.
1. Välj **diagnostik** i den **övervakning** på menyn bladet.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Välj den **typen** av mätvärdesdata för varje **service** du vill övervaka, och **bevarandeprincip** för data. Du kan också inaktivera övervakning genom att ange **Status** till **av**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Det finns två typer av mått som du kan aktivera för varje tjänst som är aktiverad som standard för nya storage-konton:

   * **Sammanställd**: samlar in mätvärden, till exempel ingång-/ utgång, tillgänglighet, svarstid och lyckade procenttal. De här måtten sammanställs för blob-, kö-, tabell- och filtjänster.
   * **Per API**: förutom sammanställd statistik, samlar in samma uppsättning mätvärden för varje Lagringsåtgärden i Azure Storage service API.

   Ange databevarandeprincip genom att flytta den **bevarande (dagar)** skjutreglaget eller ange antal dagar som data ska bevaras från 1 till 365. Standard för nya storage-konton är sju dagar. Om du inte vill ange en bevarandeprincip ange noll. Om det finns inga bevarandeprincip, är det att ta bort övervakningsdata.

   > [!WARNING]
   > Du debiteras när du manuellt ta bort mått data. Inaktuella analysdata (data som är äldre än din bevarandeprincip) tas bort av systemet utan kostnad. Vi rekommenderar en bevarandeprincip baserat på hur länge du vill behålla storage analytics-data för ditt konto. Se [vilka avgifter gör uppkommer när du aktiverar storage-mätvärden?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) för mer information.
   >

1. När du är klar med konfigurationen av övervakningen väljer **spara**.

En standarduppsättning mått visas i diagram på bladet storage-konto som enskild tjänst blad (blob, kön, tabell och filen). När du har aktiverat mått för en tjänst, kan det ta upp till en timme innan data ska visas i dess diagram. Du kan välja **redigera** på mått diagram till [konfigurera vilka mått](#how-to-customize-metrics-charts) visas i diagrammet.

Du kan inaktivera insamling av mätvärden och loggning genom att ange **Status** till **av**.

> [!NOTE]
> Azure Storage använder [tabell lagring](../common/storage-introduction.md#table-storage) att lagra mätvärden för ditt lagringskonto och lagrar mätvärdena i tabeller i ditt konto. Mer information finns i. [Hur mått lagras](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Anpassa mått diagram

Använd följande procedur för att välja vilka lagring mått att visa i ett diagram för mått. 

1. Starta genom att visa ett mått diagram för lagring i Azure-portalen. Du hittar diagram på den **lagring kontoblad** och i den **mått** bladet för en enskild tjänst (blob, kön, tabell, fil).

   I det här exemplet vi arbetar med följande diagram som visas på den **lagring kontoblad**:

   ![Val av diagram i Azure-portalen](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klicka någonstans i diagrammet för att öppna den **mått** bladet. Välj **redigera diagram** att öppna den **redigera diagram** bladet.

   ![Redigera knappen på bladet för diagram](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. På den **redigera diagram** bladet väljer den **tidsintervall** mätvärden ska visas i diagrammet, och **service** (blob, kö, tabell, filen) vars mått som du vill visa. Här har vi valt för att visa den senaste veckan mätvärden för blob-tjänsten:

   ![Intervallet och tjänsten valet av tid i bladet redigera diagram](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Välj enskilda **mått** du hade som visas i diagrammet och klickar sedan på **OK**. Till exempel här vi har valt att visa den *ContainerCount* och *ObjectCount* mått:

   ![Enskilda mått val i bladet redigera diagram](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

Diagrammet inställningarna påverkar inte samling, sammanställning och lagring av övervakningsdata i lagringskonto endast visning av mätvärdesdata.

### <a name="metrics-availability-in-charts"></a>Mått tillgänglighet i diagram

Listan över tillgängliga mått ändras baserat på vilken tjänst som du har valt i listrutan och enhetstypen för diagrammet som du redigerar. Du kan till exempel välja procentandel mått som *PercentNetworkError* och *PercentThrottlingError* bara om du redigerar ett diagram som visar enheter i procent:

![Begäran fel procentandel diagram i Azure-portalen](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Mått upplösning

Mått som du har valt i diagnostik avgör upplösning för de mätvärden som är tillgängliga för ditt konto:

* **Sammanställd** övervakning innehåller mått, till exempel ingång-/ utgång, tillgänglighet, svarstid och lyckade procenttal. De här måtten samman från blob-, tabell-, fil- och kötjänster.
* **Per API** ger ökad upplösning med mått som är tillgängliga för enskilda lagringsåtgärder utöver servicenivåer mängder.

## <a name="configure-metrics-alerts"></a>Konfigurera aviseringar för mått

Du kan skapa varningar som meddelar dig när tröskelvärdet har uppnåtts för storage resource mått.

1. Öppna den **Varningsregler bladet**, rulla ned till den **övervakning** avsnitt i den **menyn bladet** och välj **Varna regler**.
1. Välj **Lägg till avisering** att öppna den **lägga till en varningsregel** bladet
1. Välj en **resurs** (blob, fil, kö, tabell) i listrutan och ange en **namn** och **beskrivning** för din nya varningsregel.
1. Välj den **mått** för vilket du vill lägga till en varning och en avisering **villkoret**, och en **tröskelvärdet**. Tröskelvärde för enheten skriver ändras beroende på det mått som du har valt. Till exempel ”antal” är enhetstypen av för *ContainerCount*, medan enhet för den *PercentNetworkError* mått är en del.
1. Välj den **Period**. Mått som når eller överskrider tröskeln inom tiden utlösa en avisering.
1. (Valfritt) Konfigurera **e-post** och **Webhook** meddelanden. Mer information om webhooks finns [konfigurera en webhook på en Azure mått avisering](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Om du inte konfigurerar e-post eller webhook meddelanden visas aviseringar endast i Azure-portalen.

![”Lägg till en varningsregel-bladet i Azure-portalen](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Lägga till mätvärden diagram i portalens instrumentpanel

Du kan lägga till Azure Storage metrics diagram för någon av dina lagringskonton instrumentpanelen i portalen.

1. Välj Klicka **redigera instrumentpanel** när du visar instrumentpanelen i den [Azure-portalen](https://portal.azure.com).
1. I den **panelen galleriet**väljer **hitta panelerna genom** > **typen**.
1. Välj **typen** > **lagringskonton**.
1. I **resurser**, Välj lagringskonto vars mått som du vill lägga till på instrumentpanelen.
1. Välj **kategorier** > **övervakning**.
1. Dra och släpp diagrammet panelen på instrumentpanelen för måttet som visas. Upprepa för alla mått som visas på instrumentpanelen. Diagrammet ”BLOB - Totalt antal begäranden för” är markerad som ett exempel i följande bild, men alla diagram är tillgängliga för placering på instrumentpanelen.

   ![Panelen galleri i Azure-portalen](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Välj **klar anpassa** överst på instrumentpanelen när du är klar att lägga till diagram.

När du har lagt till diagram på instrumentpanelen, kan du anpassa dem enligt beskrivningen i [anpassa mått diagram](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Konfigurera loggning

Du kan instruera Azure Storage spara diagnostik för läsa, skriva och ta bort begäranden för blob-, tabell- och queue-tjänster. Databevarandeprincip som du anger gäller även dessa loggar.

> [!NOTE]
> Azure Files för närvarande stöder Storage Analytics mätvärden, men ännu stöder inte loggning.
>

1. I den [Azure-portalen](https://portal.azure.com)väljer **lagringskonton**, sedan namnet på lagringskontot för att öppna bladet storage-konto.
1. Välj **diagnostik** i den **övervakning** på menyn bladet.

    ![Diagnostik menyalternativet under övervakning i Azure-portalen.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Se till att **Status** är inställd på **på**, och välj den **services** för vilken du vill aktivera loggning.

    ![Konfigurera loggning i Azure-portalen.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Klicka på **Spara**.

Diagnostik-loggar sparas i en blobbbehållare med namnet $logs i ditt lagringskonto. Du kan visa loggdata med en lagringsutforskare som den [Microsoft Lagringsutforskaren](http://storageexplorer.com), eller via programmering med storage-klientbibliotek eller PowerShell.

Information om åtkomst till behållaren $logs finns [aktivera loggning för lagring och åtkomst till loggdata](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Nästa steg

* Hitta mer information om [statistik, loggning och fakturering](../storage-analytics.md) för Storage Analytics.
* [Aktivera Azure Storage-mätvärden och visa mätvärdesdata](../storage-enable-and-view-metrics.md) med hjälp av PowerShell och genom programmering med C#.
