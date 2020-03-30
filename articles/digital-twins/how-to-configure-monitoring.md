---
title: Konfigurerar övervakning – Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du konfigurerar övervaknings- och loggningsalternativ för Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511866"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Konfigurerar övervakning i Azure Digital Twins

Azure Digital Twins stöder robust loggning, övervakning och analys. Lösningar utvecklare kan använda Azure Monitor loggar, diagnostiska loggar, aktivitetsloggning och andra tjänster för att stödja komplexa övervakningsbehov av en IoT-app. Loggningsalternativ kan kombineras för att fråga eller visa poster över flera tjänster och för att tillhandahålla detaljerad loggningstäckning för många tjänster.

Den här artikeln sammanfattar loggnings- och övervakningsalternativ och hur du kombinerar dem på ett sätt som är specifikt för Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Granska aktivitetsloggar

[Azure-aktivitetsloggar](../azure-monitor/platform/platform-logs-overview.md) ger snabba insikter om händelse- och åtgärdshistorik på prenumerationsnivå för varje Azure-tjänstinstans.

Händelser på prenumerationsnivå omfattar:

* Skapa resurser
* Borttagning av resurser
* Skapa apphemligheter
* Integrera med andra tjänster

Aktivitetsloggning för Azure Digital Twins är aktiverad som standard och kan hittas via Azure-portalen av:

1. Välja din Azure Digital Twins-instans.
1. Välja **Aktivitetslogg** för att visa panelen:

    [![Aktivitetslogg](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

För avancerad aktivitetsloggning:

1. Välj alternativet **Loggar** för att visa **översikten över analys av aktivitetsloggen:**

    [![Val](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Översikt över aktivitetslogganalys** sammanfattar viktiga aktivitetsloggdata:

    [![Översikt över analys av aktivitetslogg]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Använd **aktivitetsloggar** för snabb insikt i händelser på prenumerationsnivå.

## <a name="enable-customer-diagnostic-logs"></a>Aktivera diagnostikloggar för kunder

[Azure-diagnostikinställningar](../azure-monitor/platform/platform-logs-overview.md) kan ställas in för varje Azure-instans för att komplettera aktivitetsloggning. Aktivitetsloggar gäller händelser på prenumerationsnivå, men diagnostisk loggning ger insikter om resursens operativa historik.

Exempel på diagnostisk loggning är:

* Körningstiden för en användardefinierad funktion
* Svarsstatuskoden för en lyckad API-begäran
* Hämta en appnyckel från ett valv

Så här aktiverar du diagnostikloggar för en instans:

1. Ta fram resursen i Azure-portalen.
1. Välj **diagnostikinställningar:**

    [![Diagnostikinställningar ett](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Välj **Aktivera diagnostik** för att samla in data (om de inte har aktiverats tidigare).
1. Fyll i de begärda fälten och välj hur och var data ska sparas:

    [![Diagnostikinställningar två](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnostikloggar sparas ofta med [Azure File Storage](../storage/files/storage-files-deployment-guide.md) och delas med Azure [Monitor-loggar](../azure-monitor/log-query/get-started-portal.md). Båda alternativen kan väljas.

>[!TIP]
>Använd **diagnostikloggar** för insikter om resursåtgärder.

## <a name="azure-monitor-and-log-analytics"></a>Azure-övervakning och logganalys

IoT-program förenar olika resurser, enheter, platser och data till en. Finkornig loggning ger detaljerad information om varje specifik del, tjänst eller komponent i den övergripande programarkitekturen, men en enhetlig översikt krävs ofta för underhåll och felsökning.

Azure Monitor innehåller den kraftfulla logganalystjänsten, som gör det möjligt att visa och analysera loggningskällor på en plats. Azure Monitor är därför mycket användbart för att analysera loggar i avancerade IoT-appar.

Exempel på användning är:

* Fråga flera diagnostiklogghistorik
* Visa loggar för flera användardefinierade funktioner
* Visa loggar för två eller flera tjänster inom en viss tidsram

Fullständig loggfrågor tillhandahålls via [Azure Monitor-loggar](../azure-monitor/log-query/log-query-overview.md). Så här konfigurerar du dessa kraftfulla funktioner:

1. Sök efter **Logganalys** i Azure-portalen.
1. Dina tillgängliga **Log Analytics-arbetsyteinstanser** visas. Välj en och välj **Loggar** att fråga:

    [![Logga analyser](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Om du inte redan har en **Log Analytics-arbetsyta** kan du skapa en arbetsyta genom att välja knappen **Lägg till:**

    [![Skapa OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

När din **Log Analytics-arbetsyteinstans** har etablerats kan du använda kraftfulla frågor för att hitta poster i multiplar eller söka med hjälp av specifika villkor med hjälp av **Logghantering:**

   [![Logghantering](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Mer information om kraftfulla frågeåtgärder finns [i komma igång med frågor](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Du kan uppleva en fördröjning på 5 minuter när du skickar händelser till **Log Analytics-arbetsytan** för första gången.

Azure Monitor-loggar ger också kraftfulla felmeddelande- och varningstjänster, som kan visas genom att välja **Diagnostisera och lösa problem:**

   [![Varnings- och felmeddelanden](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Använd **Log Analytics-arbetsytan** för att fråga logghistorik för flera appfunktioner, prenumerationer eller tjänster.

## <a name="other-options"></a>Andra alternativ

Azure Digital Twins stöder också programspecifik loggning och säkerhetsgranskning. En grundlig översikt över alla Azure-loggningsalternativ som är tillgängliga för din Azure Digital Twins-instans finns i azure [log audit-artikeln.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure-aktivitetsloggar](../azure-monitor/platform/platform-logs-overview.md).

- Djupare in i Azure-diagnostikinställningar genom att läsa en [översikt över diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

- Läs mer om [Azure Monitor-loggar](../azure-monitor/log-query/get-started-portal.md).
