---
title: Så här konfigurerar du övervakning i Azure Digital Twins | Microsoft Docs
description: Så här konfigurerar du övervakning i Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 23759a6c3d920e2b791a10ddd5ac5c5285ed1889
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926717"
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

    ![Aktivitetslogg][1]

För avancerade aktivitetsloggning:

1. Välj den **loggar** alternativet för att visa den **översikt över aktivitet Log Analytics**:

    ![Val][2]

1. Den **översikt över aktivitet Log Analytics** sammanfattar viktiga aktivitetsloggdata:

    ![Översikt över aktivitet log analytics][3]

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
1. Klicka på **diagnostikinställningar**:

    ![Diagnostikinställningar för en][4]

1. Klicka på **slå på diagnostik** att samla in data (om inte tidigare aktiverat).
1. Fyll i de begärda fälten och välj hur och var data ska sparas:

    ![Diagnostikinställningar för två][5]

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

    ![Log Analytics][6]

1. Om du inte redan har en **Log Analytics-arbetsyta** instans, kan du skapa en arbetsyta genom att klicka på den **Lägg till** knappen:

    ![Skapa OMS][7]

När din **Log Analytics-arbetsyta** instansen finns, men du kan använda kraftfulla frågor att hitta poster i multiplar loggar eller Sök med hjälp av specifika villkor med hjälp av **Logghantering**:

   ![Logghantering][8]

Läs mer om kraftfulla frågeåtgärder [komma igång med frågor](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Det uppstår en fördröjning i 5 minuter när du skickar händelser till **Log Analytics-arbetsyta** för första gången.

Azure Monitor-loggar innehåller också kraftfulla fel och avisering om tjänster, som kan visas genom att klicka på **diagnostisera och lösa problem**:

   ![Varning och fel-meddelanden][9]

>[!TIP]
>Använd **Log Analytics-arbetsyta** att fråga log historik för flera app funktioner, prenumerationer och tjänster.

## <a name="other-options"></a>Andra alternativ

Azure Digital Twins stöder också programspecifika loggning och säkerhetsgranskning. En omfattande översikt över alla Azure-loggningsalternativ som är tillgängliga för din Azure Digital Twins-instans finns i den [Azure log audit](../security/azure-log-audit.md) artikeln.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure [aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md).

- Genomgång av djupare Azure diagnostikinställningar genom att läsa en [översikt över diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md).

- Läs mer om [Azure Monitor loggar](../azure-monitor/log-query/get-started-portal.md).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
