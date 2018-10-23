---
title: Så här konfigurerar du övervakning i Azure Digital Twins | Microsoft Docs
description: Så här konfigurerar du övervakning i Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: adgera
ms.openlocfilehash: 8efb3dc1d312f42e990d4f7a2410ba8f684375c1
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651555"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Så här konfigurerar du övervakning i Azure Digital Twins

Azure Digital Twins stöder robust loggning, övervakning och analys. Lösningar för utvecklare kan använda Azure Log Analytics, diagnostikloggar, aktivitetsloggning och andra tjänster för komplexa övervakning behov av en IoT-app. Alternativ för loggning kan kombineras för att fråga efter eller visa poster över flera tjänster och ger detaljerad loggning täckning för många tjänster.

Den här artikeln sammanfattas loggning och övervakning alternativ och hur du kombinera dem på specifika sätt till Azure Digital Twins.

## <a name="review-activity-logs"></a>Granska aktivitetsloggar

Azure [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ge snabba insikter om prenumerationsnivå händelse och Åtgärd Historik för varje Azure-tjänst-instans.

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

Azure [diagnostikinställningar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) kan anges för varje Azure-instans som komplement aktivitetsloggning. Aktivitetsloggar hör till händelser på prenumerationsnivå, ger diagnostisk loggning insikter om operativa historiken för själva resurserna.

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

    Diagnostikloggar sparas ofta med hjälp av [Azure File Storage](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide) och som delas med [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal). Du kan välja båda alternativen.

>[!TIP]
>Använd **diagnostikloggar** för insikter om resursåtgärder.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor och log analytics

IoT-program förenas olika resurser, enheter, platser och data till en. Detaljerad loggning innehåller detaljerad information om varje specifik, en tjänst eller en komponent i den övergripande arkitekturen, men en enhetlig översikt krävs ofta för underhåll och felsökning.

Azure Monitor innehåller kraftfulla Log Analytics-tjänsten, vilket gör att loggning källor som ska visas och analyseras i en plats. Azure Monitor är därför mycket användbar för att analysera loggar i avancerade IoT-appar.

Exempel på användning:

* Fråga flera diagnostiklogg historik
* Se loggar för flera användardefinierade funktioner
* Visa loggar för två eller flera tjänster inom en viss tidsperiod

Fullständig log fråga är tillgängligt via [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Du ställer in de här kraftfulla funktioner:

1. Sök efter **Log Analytics** i Azure-portalen.
1. Du kommer att se din tillgängliga **Log Analytics** instanser. Välj en och välj **loggar** till fråga:

    ![Log Analytics][6]

1. Om du inte redan har en **Log Analytics** instans, kan du skapa en arbetsyta genom att klicka på den **Lägg till** knappen:

    ![Skapa OMS][7]

När din **Log Analytics** instansen finns, men du kan använda kraftfulla frågor att hitta poster i multiplar loggar eller Sök med hjälp av specifika villkor med hjälp av **Logghantering**:

   ![Logghantering][8]

Läs mer om kraftfulla frågeåtgärder [komma igång med frågor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

Azure Log Analytics erbjuder även kraftfulla fel och avisering om tjänster, som kan visas genom att klicka på **diagnostisera och lösa problem**:

   ![Varning och fel-meddelanden][9]

>[!TIP]
>Använd **Log Analytics** att fråga log historik för flera app funktioner, prenumerationer och tjänster.

## <a name="other-options"></a>Andra alternativ

Azure Digital Twins stöder också programspecifika loggning och säkerhetsgranskning. En omfattande översikt över alla Azure-loggningsalternativ som är tillgängliga för din Azure Digital Twins-instans finns i den [Azure log audit](https://docs.microsoft.com/azure/security/azure-log-audit) artikeln.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

Genomgång av djupare Azure diagnostikinställningar genom att läsa en [översikt över diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

Läs mer om [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal).

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