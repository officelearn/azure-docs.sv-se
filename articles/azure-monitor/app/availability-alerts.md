---
title: Konfigurera aviseringar för tillgänglighet med Azure Application Insights | Microsoft Docs
description: Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305240"
---
# <a name="availability-alerts"></a>Tillgänglighet aviseringar

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Det kan meddela dig om programmet inte svarar, eller om den svarar för långsamt.

## <a name="enable-alerts"></a>Aktivera aviseringar

Aviseringar aktiveras nu automatiskt som standard, men om du vill konfigurera aviseringen helt först måste du först skapa din tillgänglighetstestet.

![Skapa upplevelse](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Med den [nya enhetliga aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), varningsregel allvarlighetsgrad och notification-inställningar med [åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **måste vara** konfigurerats i aviseringarna. Utan följande steg ska får du bara meddelanden i portalen.

1. När du har sparat tillgänglighetstestet informationen om fliken Klicka på ellipsknappen av test som du just skapade. Klicka på ”Redigera avisering”.

   ![Redigera efter spara](./media/availability-alerts/edit-alert.png)

2. Ange den önskade allvarlighetsgraden, beskrivning av regel och viktigast av allt – åtgärdsgrupp som har felaviseringar som du vill använda för den här aviseringsregeln.

   ![Redigera efter spara](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Avisera om X av Y-platser som rapporterar fel

X utanför Y platser varningsregel är aktiverat som standard i den [nya aviseringar för enhetlig upplevelse](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), när du skapar en ny tillgänglighetstestet. Du kan avanmäla dig genom att välja alternativet ”klassiska” eller välja att inaktivera varningsregeln.

> [!NOTE]
> Konfigurera åtgärdsgrupper för att ta emot meddelanden när en avisering utlöses genom att följa stegen ovan. Utan det här steget endast visas i portalen meddelanden när regeln utlöses.
>

### <a name="alert-on-availability-metrics"></a>Avisera om tillgänglighetsmått

Med hjälp av den [nya enhetliga aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), du Avisera om segmenterade sammanställd tillgänglighet och testa samt varaktighet mått:

1. Välj en Application Insights-resurs i mått-upplevelsen och välj ett mått för tillgänglighet:

    ![Val av tillgänglighet-mått](./media/availability-alerts/select-metric.png)

2. Konfigurera alternativ på menyn tar dig till den nya miljön där du kan välja specifika test eller platser för att ställa in varningsregel för aviseringar. Du kan också konfigurera åtgärdsgrupper för den här aviseringsregeln här.

### <a name="alert-on-custom-analytics-queries"></a>Avisera om anpassade analysfrågor

Med hjälp av den [nya enhetliga aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), du kan meddela på [anpassade loggfrågor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Med anpassade frågor kan du meddela på eventuella valfria villkor som hjälper dig att få den mest tillförlitliga signalen av tillgänglighetsproblem. Detta gäller även särskilt, om du skickar anpassade tillgänglighetsresultat med TrackAvailability SDK. 

> [!Tip]
> Mått på tillgänglighetsdata omfattar alla anpassade tillgänglighetsresultat som du kan skicka genom att anropa vår TrackAvailability SDK. Du kan använda aviseringar vilka mått som stöds till en avisering för anpassade tillgänglighetsresultat.
>

## <a name="troubleshooting"></a>Felsökning

Dedikerad [felsökningsartikeln](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Webbtester med flera steg](availability-multistep.md)
* [URL-Pingtest web](monitor-web-app-availability.md)

