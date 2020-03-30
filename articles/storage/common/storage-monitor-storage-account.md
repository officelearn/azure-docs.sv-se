---
title: Så här övervakar du ett Azure Storage-konto i Azure-portalen | Microsoft-dokument
description: Lär dig hur du övervakar ett lagringskonto i Azure med hjälp av Azure-portalen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268293"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Övervaka ett lagringskonto i Azure-portalen

[Azure Storage Analytics](storage-analytics.md) tillhandahåller mått för alla lagringstjänster och loggar för blobbar, köer och tabeller. Du kan använda [Azure-portalen](https://portal.azure.com) för att konfigurera vilka mått och loggar som registreras för ditt konto och konfigurera diagram som ger visuella representationer av dina måttdata. 

Vi rekommenderar att du granskar [Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (förhandsversion). Det är en funktion i Azure Monitor som erbjuder omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage-tjänsters prestanda, kapacitet och tillgänglighet. Det kräver inte att du aktiverar eller konfigurerar något, och du kan omedelbart visa dessa mått från de fördefinierade interaktiva diagrammen och andra visualiseringar som ingår.

> [!NOTE]
> Det finns kostnader för att undersöka övervakningsdata i Azure-portalen. Mer information finns i [Storage Analytics](storage-analytics.md).
>
> Azure Files stöder för närvarande Storage Analytics-mått, men stöder ännu inte loggning.
>
> Premium prestandablock blob lagringskonton stöder inte Lagring analytiska mått men de stöder loggning. Du kan aktivera loggning programmässigt via REST API eller klientbiblioteket. Om du vill visa mått med premiumprestanda blob blob lagringskonton, överväg att använda [Azure Storage Metrics i Azure Monitor](storage-metrics-in-azure-monitor.md).
>
> En detaljerad guide om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Problem med Azure Storage finns i [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurera övervakning för ett lagringskonto

1. I [Azure-portalen](https://portal.azure.com)väljer du **Lagringskonton**och sedan lagringskontonamnet för att öppna kontoinstrumentpanelen.
1. Välj **Diagnostik** i **avsnittet ÖVERVAKNING** i menybladet.

    ![ÖvervakningOptioner](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Välj **typ** av måttdata för varje **tjänst** som du vill övervaka och **bevarandeprincipen** för data. Du kan också inaktivera övervakning genom att ställa in **Status** på **Av**.

    ![ÖvervakningOptioner](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Om du vill ange datalagringsprincipen flyttar du skjutreglaget **Kvarhållning (dagar)** eller anger antalet dagar med data som ska behållas, från 1 till 365. Standardvärdet för nya lagringskonton är sju dagar. Om du inte vill ange en bevarandeprincip anger du noll. Om det inte finns någon bevarandeprincip är det upp till dig att ta bort övervakningsdata.

   > [!WARNING]
   > Du debiteras när du manuellt tar bort måttdata. Inaktuella analysdata (data som är äldre än din lagringsprincip) tas bort av systemet utan kostnad. Vi rekommenderar att du anger en bevarandeprincip baserat på hur länge du vill behålla lagringsanalysdata för ditt konto. Mer information [finns i Fakturering på lagringsmått.](storage-analytics-metrics.md#billing-on-storage-metrics)
   >

1. När du är klar med övervakningskonfigurationen väljer du **Spara**.

En standarduppsättning mått visas i diagram på lagringskontobladet, liksom de enskilda tjänstbladen (blob, kö, tabell och fil). När du har aktiverat mått för en tjänst kan det ta upp till en timme innan data visas i diagrammen. Du kan välja **Redigera** i alla måttdiagram för att konfigurera vilka mått som visas i diagrammet.

Du kan inaktivera statistikinsamling och loggning genom att ange **Status** till **Av**.

> [!NOTE]
> Azure Storage använder [tabelllagring](storage-introduction.md#table-storage) för att lagra måtten för ditt lagringskonto och lagrar måtten i tabeller i ditt konto. Mer information finns här: [Hur mått lagras](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Anpassa måttdiagram

Använd följande procedur för att välja vilka lagringsmått som ska visas i ett måttdiagram.

1. Börja med att visa ett lagringsmåttdiagram i Azure-portalen. Du kan hitta diagram på **lagringskontobladet** och i **bladet Mått** för en enskild tjänst (blob, kö, tabell, fil).

   I det här exemplet använder du följande diagram som visas på **lagringskontobladet:**

   ![Diagramval i Azure-portalen](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klicka var som helst i diagrammet för att redigera diagrammet.

1. Välj sedan **tidsintervallet** för de mått som ska visas i diagrammet och **tjänsten** (blob, kö, tabell, fil) vars mått du vill visa. Här väljs den senaste veckans mått för att visa för blob-tjänsten:

   ![Tidsintervall och serviceval i bladet Redigera diagram](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Markera de enskilda **mått** som du vill visa i diagrammet och klicka sedan på **OK**.

   ![Individuellt måttval i bladet Redigera diagram](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Diagraminställningarna påverkar inte insamling, aggregering eller lagring av övervakningsdata i lagringskontot.

### <a name="metrics-availability-in-charts"></a>Tillgänglighet för mått i diagram

Listan över tillgängliga mått ändras baserat på vilken tjänst du har valt i listrutan och enhetstypen för diagrammet som du redigerar. Du kan till exempel välja procentmått som *PercentNetworkError* och *PercentThrottlingError* endast om du redigerar ett diagram som visar enheter i procent:

![Begärandeprocentdiagram i Azure-portalen](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Upplösning av mått

De mått som du har valt i **Diagnostik** avgör upplösningen för de mått som är tillgängliga för ditt konto:

* **Aggregerad** övervakning ger mått som ingående/utgående, tillgänglighet, svarstid och framgångsprocent. Dessa mått aggregeras från blob-, tabell-, fil- och kötjänsterna.
* **Per API** ger finare upplösning, med mått som är tillgängliga för enskilda lagringsåtgärder, utöver aggregat på servicenivå.

## <a name="configure-metrics-alerts"></a>Konfigurera måttaviseringar

Du kan skapa aviseringar för att meddela dig när tröskelvärden har uppnåtts för lagringsresursmått.

1. Om du vill öppna **bladet Varningsregler**bläddrar du ned till avsnittet **ÖVERVAKNING** i **menybladet** och väljer **Aviseringar (klassiskt).**
2. Välj **Lägg till måttavisering (klassisk)** för att öppna bladet **Lägg till en varningsregel**
3. Ange ett **namn** och **en beskrivning** för den nya aviseringsregeln.
4. Välj det **mått** som du vill lägga till en avisering för, ett **varningsvillkor**och ett **tröskelvärde**. Tröskelenhetstypen ändras beroende på vilket mått du har valt. Till exempel är "antal" enhetstypen för *ContainerCount*, medan enheten för *Måttet PercentNetworkError* är en procentsats.
5. Välj **period**. Mått som når eller överskrider tröskelvärdet inom perioden utlöser en avisering.
6. (Valfritt) Konfigurera **e-post-** och **Webhook-meddelanden.** Mer information om webhooks finns i [Konfigurera en webhook på en Azure-måttavisering](../../azure-monitor/platform/alerts-webhooks.md). Om du inte konfigurerar e-post- eller webhook-meddelanden visas aviseringar endast i Azure-portalen.

![Bladet "Lägg till en varningsregel" i Azure-portalen](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Lägga till måttdiagram på portalinstrumentpanelen

Du kan lägga till Azure Storage-måttdiagram för alla dina lagringskonton på portalinstrumentpanelen.

1. Välj klicka på **Redigera instrumentpanel** när du visar instrumentpanelen i [Azure-portalen](https://portal.azure.com).
1. I **panelgalleriet**väljer du **Sök paneler efter** > **typ**.
1. Välj **Typ** > **lagringskonton**.
1. I **Resurser**väljer du det lagringskonto vars mått du vill lägga till i instrumentpanelen.
1. Välj **Kategorier** > **Övervakning**.
1. Dra och släpp diagrampanelen på instrumentpanelen för det mått som du vill ska visas. Upprepa för alla mått som du vill visas på instrumentpanelen. I följande bild markeras diagrammet "Blobbar - Totalt antal begäranden" som ett exempel, men alla diagram är tillgängliga för placering på instrumentpanelen.

   ![Panelgalleri i Azure-portalen](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Välj **Klar med anpassningen** högst upp på instrumentpanelen när du är klar med att lägga till diagram.

När du har lagt till diagram på instrumentpanelen kan du anpassa dem ytterligare enligt beskrivningen i Anpassa måttdiagram.

## <a name="configure-logging"></a>Konfigurera loggning

Du kan instruera Azure Storage att spara diagnostikloggar för läs-, skriv- och borttagningsbegäranden för blob-, table- och kötjänsterna. Den datalagringsprincip som du anger gäller även för dessa loggar.

> [!NOTE]
> Azure Files stöder för närvarande Storage Analytics-mått, men stöder ännu inte loggning.
>

1. I [Azure-portalen](https://portal.azure.com)väljer du **Lagringskonton**och sedan namnet på lagringskontot för att öppna lagringskontobladet.
1. Välj **Diagnostikinställningar (klassisk)** i avsnittet **Övervakning (klassiskt)** i menybladet.

    ![Menyalternativ för diagnostik under ÖVERVAKNING i Azure-portalen.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Kontrollera **Status** status är inställt **på På**och välj de **tjänster** som du vill aktivera loggning för.

    ![Konfigurera loggning i Azure-portalen.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klicka på **Spara**.

Diagnostikloggarna sparas i en blob-behållare med namnet *$logs* i ditt lagringskonto. Du kan visa loggdata med hjälp av en lagringsutforskaren som [Utforskaren](https://storageexplorer.com)i Microsoft Storage Eller programmässigt med hjälp av lagringsklientbiblioteket eller PowerShell.

Information om hur du kommer åt $logs-behållaren finns i [Loggning av lagringsanalys](storage-analytics-logging.md).

## <a name="next-steps"></a>Nästa steg

* Hitta mer information om [mått, loggning och fakturering](storage-analytics.md) för Storage Analytics.
