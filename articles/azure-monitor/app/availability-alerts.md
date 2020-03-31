---
title: Konfigurera tillgänglighetsaviseringar med Azure Application Insights | Microsoft-dokument
description: Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: e0c1a93ef663762bec199abc5aa7eabbc821168d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654961"
---
# <a name="availability-alerts"></a>Tillgänglighetsaviseringar

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Det kan varna dig om ditt program inte svarar, eller om det svarar för långsamt.

## <a name="enable-alerts"></a>Aktivera aviseringar

Aviseringar aktiveras nu automatiskt som standard, men för att konfigurera aviseringen helt måste du först skapa ditt tillgänglighetstest.

![Skapa upplevelse](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Med de [nya enhetliga aviseringarna](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)måste allvarlighetsgraden för varningsregeln och meddelandeinställningarna med [åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **konfigureras** i aviseringsupplevelsen. Utan följande steg får du bara meddelanden i portalen.

1. Efter att ha sparat tillgänglighetstestet, klicka på ellipsen på fliken Information genom det test du just gjort. Klicka på "redigera varning".

   ![Redigera efter sparad](./media/availability-alerts/edit-alert.png)

2. Ange önskad allvarlighetsgrad, regelbeskrivning och viktigast av allt - den åtgärdsgrupp som har de meddelandeinställningar som du vill använda för den här aviseringsregeln.

   ![Redigera efter sparad](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Varning på X från Y-platser som rapporterar fel

Varningsregeln X från Y-platser är aktiverad som standard i den [nya enhetliga aviseringsupplevelsen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)när du skapar ett nytt tillgänglighetstest. Du kan välja bort genom att välja alternativet "klassiskt" eller välja att inaktivera varningsregeln.

> [!NOTE]
> Konfigurera åtgärdsgrupperna så att de får meddelanden när aviseringen utlöses genom att följa stegen ovan. Utan det här steget får du bara meddelanden i portalen när regeln utlöses.
>

### <a name="alert-on-availability-metrics"></a>Avisering om tillgänglighetsmått

Med hjälp av [de nya enhetliga aviseringarna](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)kan du också avisera segmenterade aggregerade tillgänglighets- och testvaraktighetsmått:

1. Välj en application insights-resurs i måttupplevelsen och välj ett tillgänglighetsmått:

    ![Val av tillgänglighetsmått](./media/availability-alerts/select-metric.png)

2. Alternativet Konfigurera aviseringar från menyn tar dig till den nya upplevelsen där du kan välja specifika tester eller platser att ställa in varningsregel på. Du kan också konfigurera åtgärdsgrupperna för den här aviseringsregeln här.

### <a name="alert-on-custom-analytics-queries"></a>Avisering om anpassade analysfrågor

Med hjälp av de [nya enhetliga aviseringarna](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)kan du avisera [om anpassade loggfrågor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Med anpassade frågor kan du avisera om alla godtyckliga villkor som hjälper dig att få den mest tillförlitliga signalen om tillgänglighetsproblem. Detta gäller även om du skickar anpassade tillgänglighetsresultat med hjälp av TrackAvailability SDK.

> [!Tip]
> Måtten på tillgänglighetsdata inkluderar alla anpassade tillgänglighetsresultat som du kan skicka in genom att anropa vår TrackAvailability SDK. Du kan använda avisering av måttstöd för att avisera anpassade tillgänglighetsresultat.
>

## <a name="automate-alerts"></a>Automatisera aviseringar

Om du vill automatisera den här processen med Azure Resource Manager-mallar läser du mallen Skapa ett mått med dokumentation [av Resource Manager-mallen.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="troubleshooting"></a>Felsökning

Särskild [felsökningsartikel](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Webbtester med flera steg](availability-multistep.md)
* [Url ping webbtester](monitor-web-app-availability.md)
