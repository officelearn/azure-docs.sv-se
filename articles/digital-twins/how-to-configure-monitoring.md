---
title: Så här konfigurerar du övervakning – Azure Digitals flätar | Microsoft Docs
description: Lär dig hur du konfigurerar övervaknings-och loggnings alternativ för Azure Digitals dubbla.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: ed376a3f500f6d6af3d0eab7f98b68e856513600
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547118"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Så här konfigurerar du övervakning i digitala Azure-dubbla

Azure Digitals dubbla har stöd för robust loggning, övervakning och analys. Lösningar utvecklare kan använda Azure Monitor loggar, diagnostikloggar, aktivitets loggning och andra tjänster för att stödja de komplexa övervaknings behoven i en IoT-app. Loggnings alternativ kan kombineras för att fråga eller Visa poster över flera tjänster och för att tillhandahålla detaljerad loggning för många tjänster.

I den här artikeln sammanfattas loggnings-och övervaknings alternativ och hur du kombinerar dem på olika sätt för Azure digitala dubbla.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Granska aktivitets loggar

Azure [aktivitets loggar](../azure-monitor/platform/activity-logs-overview.md) ger snabba insikter om händelse-och åtgärds historik på prenumerations nivå för varje Azure-tjänstinstans.

Händelser på prenumerations nivå är:

* Skapa resurs
* Borttagning av resurs
* Skapa program hemligheter
* Integrera med andra tjänster

Aktivitets loggning för digitala Azure-dubbla objekt är aktiverat som standard och kan hittas via Azure Portal av:

1. Välja din Azure Digital-instansen.
1. Välj **aktivitets logg** för att öppna panelen Visa:

    [![aktivitets logg](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

För Avancerad aktivitets loggning:

1. Välj alternativet **loggar** för att visa **Aktivitetslogganalys översikt**:

    [![val](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Aktivitetslogganalys översikten** sammanfattar viktiga aktivitets logg data:

    [Översikt över ![aktivitets logg analys]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Använd **aktivitets loggar** för att snabbt insikter om händelser på prenumerations nivå.

## <a name="enable-customer-diagnostic-logs"></a>Aktivera kunders diagnostikloggar

Azure [Diagnostic-inställningar](../azure-monitor/platform/resource-logs-overview.md) kan ställas in för varje Azure-instans för att komplettera aktivitets loggning. När aktivitets loggar rör händelser på prenumerations nivå, ger diagnostisk loggning insikter om arbets historiken för resurserna själva.

Exempel på diagnostisk loggning är:

* Körnings tiden för en användardefinierad funktion
* Svars status koden för en lyckad API-begäran
* Hämta en app-nyckel från ett valv

Så här aktiverar du diagnostikloggar för en instans:

1. Ta upp resursen i Azure Portal.
1. Välj **diagnostikinställningar**:

    [![diagnostikinställningar en](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Välj **Aktivera diagnostik** för att samla in data (om de inte redan har Aktiver ATS).
1. Fyll i de begärda fälten och välj hur och var data ska sparas:

    [![diagnostikinställningar två](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnostikloggar sparas ofta med hjälp av [Azure File Storage](../storage/files/storage-files-deployment-guide.md) och delas med [Azure Monitor loggar](../azure-monitor/log-query/get-started-portal.md). Båda alternativen kan väljas.

>[!TIP]
>Använd **diagnostikloggar** för att insikter om resurs åtgärder.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor och Log Analytics

IoT-program enhets olika resurser, enheter, platser och data till en. Detaljerad loggning ger detaljerad information om varje specifik del, tjänst eller komponent i den övergripande program arkitekturen, men en enhetlig översikt krävs ofta för underhåll och fel sökning.

Azure Monitor innehåller den kraftfulla Log Analytics-tjänsten, som gör att loggnings källor kan visas och analyseras på en plats. Azure Monitor är därför mycket användbart för att analysera loggar i avancerade IoT-appar.

Exempel på användning är:

* Fråga efter flera diagnostiska logg historik
* Se loggar för flera användardefinierade funktioner
* Visa loggar för två eller fler tjänster inom en angiven tids period

Fullständig logg fråga tillhandahålls via [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md). Så här konfigurerar du dessa kraftfulla funktioner:

1. Sök efter **Log Analytics** i Azure Portal.
1. Du kommer att se dina tillgängliga **Log Analytics arbets ytans** instanser. Välj en och välj **loggar** att fråga:

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Om du inte redan har en **Log Analytics arbets ytans** instans kan du skapa en arbets yta genom att välja knappen **Lägg till** :

    [![skapa OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

När din **Log Analytics arbets ytan** har allokerats kan du använda kraftfulla frågor för att hitta poster i flera loggar eller söka med hjälp av ett särskilt villkor med **logg hantering**:

   [![logg hantering](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Mer information om kraftfulla fråge åtgärder finns i [komma igång med frågor](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Det kan uppstå en fördröjning på fem minuter när du skickar händelser till **Log Analytics arbets yta** för första gången.

Azure Monitor loggar tillhandahåller också kraftfulla fel och aviserings aviserings tjänster, som kan visas genom att välja **diagnostisera och lösa problem**:

   [![aviseringar och fel meddelanden](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Använd **Log Analytics arbets yta** för att söka efter logg historik för flera app-funktioner, prenumerationer eller tjänster.

## <a name="other-options"></a>Andra alternativ

Azure Digitals dubbla, stöder också programspecifik loggning och säkerhets granskning. En grundlig översikt över alla Azure-loggnings alternativ som är tillgängliga för Azure Digital-instansen finns i artikeln [Azure logg granskning](../security/fundamentals/log-audit.md) .

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azures [aktivitets loggar](../azure-monitor/platform/activity-logs-overview.md).

- Lär dig mer om Azure Diagnostic-inställningar genom [att läsa en översikt över diagnostikloggar](../azure-monitor/platform/resource-logs-overview.md).

- Läs mer om [Azure Monitor loggar](../azure-monitor/log-query/get-started-portal.md).
