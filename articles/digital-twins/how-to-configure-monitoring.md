---
title: Så här konfigurerar du övervakning i Azure Digital Twins | Microsoft Docs
description: Så här konfigurerar du övervakning i Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 5dc2587a0c127106d5afb41e20eca43919065f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118789"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Så här konfigurerar du övervakning i Azure Digital Twins

Azure Digital Twins stöder robust loggning, övervakning och analys. Lösningar för utvecklare kan använda Azure Monitor-loggar, diagnostikloggar, aktivitetsloggning och andra tjänster för komplexa övervakning behov av en IoT-app. Alternativ för loggning kan kombineras för att fråga efter eller visa poster över flera tjänster och ger detaljerad loggning täckning för många tjänster.

Den här artikeln sammanfattas loggning och övervakning alternativ och hur du kombinera dem på specifika sätt till Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Granska aktivitetsloggar

Azure [aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md) ge snabba insikter om prenumerationsnivå händelse och Åtgärd Historik för varje Azure-tjänst-instans.

Händelser på prenumerationsnivå är:

* Skapa en resurs
* Ta bort resurs
* Skapa apphemligheter
* Integrera med andra tjänster

Aktivitetsloggning för Azure Digital Twins är aktiverat som standard och kan hittas via Azure portal genom att:

1. Att välja din digitala Twins för Azure-instans.
1. Välja **aktivitetsloggen** att ta fram panelen Visa:

    [![Aktivitetslogg](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

För avancerade aktivitetsloggning:

1. Välj den **loggar** alternativet för att visa den **översikt över aktivitet Log Analytics**:

    [![Val av](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. Den **översikt över aktivitet Log Analytics** sammanfattar viktiga aktivitetsloggdata:

    [![Översikt över aktivitet log analytics]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Använd **aktivitetsloggar** för snabba insikter om händelser på prenumerationsnivå.

## <a name="enable-customer-diagnostic-logs"></a>Aktivera diagnostikloggar för kunden

Azure [diagnostikinställningar](../azure-monitor/platform/diagnostic-logs-overview.md) kan anges för varje Azure-instans som komplement aktivitetsloggning. Aktivitetsloggar hör till händelser på prenumerationsnivå, ger diagnostisk loggning insikter om operativa historiken för själva resurserna.

Exempel på Diagnostisk loggning:

* Körningstid för en användardefinierad funktion
* Svarets statuskod för en lyckad API-begäran
* Hämtar en app-nyckel från ett valv

Aktivera diagnostikloggar för en instans:

1. Ta fram resursen i Azure-portalen.
1. Välj **diagnostikinställningar**:

    [![Diagnostikinställningar för en](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Välj **slå på diagnostik** att samla in data (om inte tidigare aktiverat).
1. Fyll i de begärda fälten och välj hur och var data ska sparas:

    [![Diagnostikinställningar för två](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnostikloggar sparas ofta med hjälp av [Azure File Storage](../storage/files/storage-files-deployment-guide.md) och som delas med [Azure Monitor loggar](../azure-monitor/log-query/get-started-portal.md). Du kan välja båda alternativen.

>[!TIP]
>Använd **diagnostikloggar** för insikter om resursåtgärder.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor och log analytics

IoT-program förenas olika resurser, enheter, platser och data till en. Detaljerad loggning innehåller detaljerad information om varje specifik, en tjänst eller en komponent i den övergripande arkitekturen, men en enhetlig översikt krävs ofta för underhåll och felsökning.

Azure Monitor innehåller kraftfulla log analytics-tjänsten, vilket gör att loggning källor för att visas och analyseras i en plats. Azure Monitor är därför mycket användbar för att analysera loggar i avancerade IoT-appar.

Exempel på användning:

* Fråga flera diagnostiklogg historik
* Se loggar för flera användardefinierade funktioner
* Visa loggar för två eller flera tjänster inom en viss tidsperiod

Fullständig log fråga är tillgängligt via [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md). Du ställer in de här kraftfulla funktioner:

1. Sök efter **Log Analytics** i Azure-portalen.
1. Du kommer att se din tillgängliga **Log Analytics-arbetsyta** instanser. Välj en och välj **loggar** till fråga:

    [![Log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Om du inte redan har en **Log Analytics-arbetsyta** instans, kan du skapa en arbetsyta genom att välja den **Lägg till** knappen:

    [![Skapa OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

När din **Log Analytics-arbetsyta** instansen finns, men du kan använda kraftfulla frågor att hitta poster i multiplar loggar eller Sök med hjälp av specifika villkor med hjälp av **Logghantering**:

   [![Logghantering](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Läs mer om kraftfulla frågeåtgärder [komma igång med frågor](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Det uppstår en fördröjning i 5 minuter när du skickar händelser till **Log Analytics-arbetsyta** för första gången.

Azure Monitor-loggar erbjuder även kraftfulla fel och avisering om tjänster, som kan visas genom att välja **diagnostisera och lösa problem**:

   [![Varning och fel-meddelanden](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Använd **Log Analytics-arbetsyta** att fråga log historik för flera app funktioner, prenumerationer och tjänster.

## <a name="other-options"></a>Andra alternativ

Azure Digital Twins stöder också programspecifika loggning och säkerhetsgranskning. En omfattande översikt över alla Azure-loggningsalternativ som är tillgängliga för din Azure Digital Twins-instans finns i den [Azure log audit](../security/azure-log-audit.md) artikeln.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure [aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md).

- Genomgång av djupare Azure diagnostikinställningar genom att läsa en [översikt över diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md).

- Läs mer om [Azure Monitor loggar](../azure-monitor/log-query/get-started-portal.md).
