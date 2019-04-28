---
title: Övervaka ett Azure Storage-konto | Microsoft Docs
description: Lär dig hur du övervakar ett storage-konto i Azure med hjälp av Azure portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 64cfac0d689df88c4d432e772bcd0a0cc7ab4ade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478223"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Övervaka ett lagringskonto i Azure portal

[Azure Storage Analytics](storage-analytics.md) tillhandahåller mått för alla lagringstjänster och loggar för blobbar, köer och tabeller. Du kan använda den [Azure-portalen](https://portal.azure.com) konfigurera vilka mått och loggar sparas för ditt konto och konfigurera diagram som ger visuella representationer av dina måttdata.

> [!NOTE]
> Det finns kostnader i samband med att undersöka övervakningsdata i Azure-portalen. Mer information finns i [Lagringsanalys](storage-analytics.md).
>
> Azure Files kan du för närvarande stöder Storage Analytics mätvärden, men har stöd inte för loggning.
>
> Utförliga instruktioner om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem, se [övervaka, diagnostisera och Felsök Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurera övervakning för ett lagringskonto

1. I den [Azure-portalen](https://portal.azure.com)väljer **lagringskonton**, sedan namnet på lagringskontot att öppna instrumentpanelen för kontot.
1. Välj **diagnostik** i den **övervakning** på menyn-bladet.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Välj den **typ** av mätvärden för var och en **service** du vill övervaka, och **bevarandeprincip** för data. Du kan också inaktivera övervakning genom att ange **Status** till **av**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Om du vill ange databevarandeprincip, flytta den **Kvarhållning (dagar)** skjutreglaget eller ange antalet dagar för att behålla kan mellan 1 och 365. Standard för nya lagringskonton som är sju dagar. Om du inte vill ange en kvarhållningsprincip ange noll. Om det finns ingen bevarandeprincip, är det upp till dig att ta bort övervakningsdata.

   > [!WARNING]
   > Du debiteras när du manuellt ta bort mätvärden. Inaktuella analytics-data (data äldre än bevarandeprincipen) tas bort av systemet utan kostnad. Vi rekommenderar att en bevarandeprincip som baseras på hur länge du vill behålla storage analytics-data för ditt konto. Se [faktureringen för lagringsmått](storage-analytics-metrics.md#billing-on-storage-metrics) för mer information.
   >

1. När du är klar med konfigurationen av övervakningen väljer **spara**.

En standarduppsättning av mått visas i diagrammen på bladet för storage-konto, samt enskilda tjänstens blad (blob, kö, tabell och fil). När du har aktiverat mått för en tjänst, kan det ta upp till en timme innan data visas i dess diagram. Du kan välja **redigera** på mått diagrammet om du vill konfigurera vilka mått visas i diagrammet.

Du kan inaktivera insamling av mätvärden och loggning genom att ange **Status** till **av**.

> [!NOTE]
> Azure Storage använder [tabellagring](storage-introduction.md#table-storage) att lagra mått för ditt lagringskonto och lagrar mått i tabeller i ditt konto. Mer information finns i. [Hur mått lagras](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Anpassa måttdiagram

Använd följande procedur för att välja vilka storage-mått att visa i ett diagram med prestandamått.

1. Starta genom att visa ett måttdiagram för lagring i Azure-portalen. Du kan hitta diagram på den **bladet lagringskonto** och i den **mått** bladet för en enskild tjänst (blob, kö, tabell, fil).

   I det här exemplet använder du följande diagram visas på den **bladet lagringskonto**:

   ![Val av diagram i Azure-portalen](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klicka någonstans i diagrammet för att redigera diagrammet.

1. Välj sedan den **tidsintervall** mått att visa i diagrammet, och **service** (blob, kö, tabell, fil) vars mått som du vill visa. Här, markeras den senaste veckan mått att visa för blob-tjänsten:

   ![Intervall och tjänsten val av tid på bladet redigera diagram](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Välj enskilda **mått** du hade som visas i diagrammet och klicka sedan på **OK**.

   ![Enskilda måttval redigera diagram bladet](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Inställningar för din påverkar inte den samling, aggregering eller lagring av övervakningsdata i lagringskontot.

### <a name="metrics-availability-in-charts"></a>Mått tillgänglighet i diagram

Lista över tillgängliga mått ändras baserat på vilken tjänst som du har valt i listrutan och enhetstypen för diagrammet som du redigerar. Du kan till exempel välja procent mått som *PercentNetworkError* och *PercentThrottlingError* endast om du redigerar ett diagram som visar enheter i procent:

![Begäran fel procent diagram i Azure portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Mått-lösning

De mått som du valde i **diagnostik** upplösning mätvärden som är tillgängliga för ditt konto:

* **Sammanställd** övervakning ger mått, till exempel ingående/utgående trafik, tillgänglighet, svarstid och framgångsprocent. De här måtten sammanställs från blob-, tabell-, fil- och kötjänster.
* **Per API** ger bättre matchning med mått som är tillgängliga för enskilda lagringsåtgärder utöver tjänstnivå aggregeringar.

## <a name="configure-metrics-alerts"></a>Konfigurera aviseringar för mått

Du kan skapa varningar som meddelar dig när tröskelvärdet har uppnåtts för mätvärden i storage resurs.

1. Öppna den **Varningsregler bladet**, rulla ned till den **övervakning** delen av den **Appmenyns blad** och välj **aviseringar (klassisk)**.
2. Välj **Lägg till måttavisering (klassisk)** att öppna den **Lägg till en varningsregel** bladet
3. Ange en **namn** och **beskrivning** för din nya aviseringsregeln.
4. Välj den **mått** för vilket du vill lägga till en avisering en avisering **villkor**, och en **tröskelvärdet**. Tröskelvärde för enheten skriver ändras beroende på mått som du har valt. Till exempel ”antal” är enhetstypen av för *ContainerCount*, medan enheten för den *PercentNetworkError* mått är en procentandel.
5. Välj den **Period**. Mått som når eller överskrider tröskelvärdet inom utlösa en avisering.
6. (Valfritt) Konfigurera **e-post** och **Webhook** meddelanden. Läs mer på webhooks [konfigurera en webhook i en Azure metrisk varning](../../azure-monitor/platform/alerts-webhooks.md). Om du inte konfigurerar e-post eller webhook-meddelande visas aviseringar endast i Azure-portalen.

![”Lägg till en varningsregel” bladet i Azure portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Lägg till måttdiagram i instrumentpanelen i portalen

Du kan lägga till Azure Storage-måttdiagram för någon av dina lagringskonton på portalens instrumentpanel.

1. Välj Klicka **redigera instrumentpanel** medan du visar instrumentpanelen i den [Azure-portalen](https://portal.azure.com).
1. I den **Panelgalleriet**väljer **Sök paneler av** > **typ**.
1. Välj **typ** > **lagringskonton**.
1. I **resurser**, Välj lagringskonto vars mått som du vill lägga till på instrumentpanelen.
1. Välj **kategorier** > **övervakning**.
1. Dra och släpp diagrammet panelen på instrumentpanelen för måttet som visas. Upprepa för alla mått som visas på instrumentpanelen. I följande bild, ”Blobbar - Totalt antal begäranden” diagrammet är markerat som ett exempel, men alla diagram är tillgängliga för placering på instrumentpanelen.

   ![Panelgalleriet i Azure-portalen](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Välj **anpassningen är klar** överst på instrumentpanelen när du är klar att lägga till diagram.

När du har lagt till diagram på instrumentpanelen, kan du anpassa dem ytterligare enligt beskrivningen i Anpassa måttdiagram.

## <a name="configure-logging"></a>Konfigurera loggning

Du kan instruera Azure Storage för att spara diagnostikloggar för läsa, skriva och ta bort begäranden för blob-, tabell- och kötjänster. Policyn för datalagring i du ställer in gäller även för de här loggarna.

> [!NOTE]
> Azure Files kan du för närvarande stöder Storage Analytics mätvärden, men har stöd inte för loggning.
>

1. I den [Azure-portalen](https://portal.azure.com)väljer **lagringskonton**, sedan namnet på lagringskontot för att öppna bladet för lagringskontot.
1. Välj **diagnostik** i den **övervakning** på menyn-bladet.

    ![Diagnostik menyalternativet under övervakning i Azure-portalen.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Se till att **Status** är inställd på **på**, och välj den **services** för vilket du vill aktivera loggning.

    ![Konfigurera loggning i Azure-portalen.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klicka på **Spara**.

Diagnostik-loggarna sparas i en blobbehållare med namnet *$logs* i ditt lagringskonto. Du kan visa loggdata med en lagringsutforskare som den [Microsoft Lagringsutforskaren](https://storageexplorer.com), eller via programmering med storage-klientbibliotek eller PowerShell.

Läs om hur åtkomst till behållaren $logs [loggningen i Storage analytics](storage-analytics-logging.md).

## <a name="next-steps"></a>Nästa steg

* Hitta mer information om [mätvärden, loggning och fakturering](storage-analytics.md) för Storage Analytics.
