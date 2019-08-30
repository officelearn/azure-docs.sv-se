---
title: Översikt över Azures aktivitets logg
description: Lär dig mer om Azure aktivitets loggen och hur du kan använda den för att förstå händelser som inträffar i din Azure-prenumeration.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fa1737a8627fe9561a2a84e7f0ef69aefb6deb14
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170624"
---
# <a name="overview-of-azure-activity-log"></a>Översikt över Azure aktivitets logg

**Azure aktivitets loggen** ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Detta omfattar en mängd data, från Azure Resource Manager användnings data till uppdateringar på Service Health händelser. Aktivitets loggen kallades tidigare för _gransknings loggar_ eller _drift loggar_, eftersom den administrativa kategorin rapporterar kontroll Plans händelser för dina prenumerationer. 

Använd aktivitets loggen för att fastställa _vad_, _vem_och _när_ för Skriv åtgärder (skicka, skicka och ta bort) som ska vidtas för resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. 

Aktivitets loggen innehåller inte Läs-(GET)-åtgärder eller åtgärder för resurser som använder klassisk/RDFE-modell.

## <a name="comparison-to-diagnostic-logs"></a>Jämförelse med diagnostikloggar
Det finns en enda aktivitets logg för varje Azure-prenumeration. Den ger information om åtgärder på en resurs från utsidan ("kontroll planet"). [Diagnostikloggar](diagnostic-logs-overview.md) genereras av en resurs och ger information om hur resursen fungerar ("data planet"). Du måste aktivera diagnostikinställningar för varje resurs.

![Aktivitets loggar jämfört med diagnostikloggar](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure aktivitets loggen är främst avsedd för aktiviteter som förekommer i Azure Resource Manager. Den spårar inte resurser med hjälp av klassisk/RDFE-modell. Vissa klassiska resurs typer har en proxy-resurs leverantör i Azure Resource Manager (till exempel Microsoft. ClassicCompute). Om du interagerar med en klassisk resurs typ via Azure Resource Manager med hjälp av dessa providers-proxyservrar, visas åtgärderna i aktivitets loggen. Om du interagerar med en klassisk resurs typ utanför Azure Resource Manager-proxyservrar registreras dina åtgärder bara i åtgärds loggen. Du kan bläddra i åtgärds loggen i ett separat avsnitt i portalen.

## <a name="activity-log-retention"></a>Kvarhållning av aktivitets logg
När du har skapat aktivitets logg poster ändras eller tas de inte bort av systemet. Du kan inte heller ändra dem i gränssnittet eller program mässigt. Aktivitets logg händelser lagras i 90 dagar. För att lagra dessa data under längre perioder [samlar du in dem i Azure Monitor](activity-log-collect.md) eller [exporterar den till lagrings-eller Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Visa aktivitets loggen
Visa aktivitets loggen för alla resurser på **Monitor** -menyn i Azure Portal. Visa aktivitets loggen för en viss resurs från alternativet **aktivitets logg** på den resurs menyn. Du kan också hämta aktivitets logg poster med PowerShell, CLI eller REST API.  Se [Visa och hämta Azure aktivitets logg händelser](activity-log-view.md).

![Visa aktivitets logg](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Samla in aktivitets logg i Azure Monitor
Samla in aktivitets loggen på en Log Analytics arbets yta i Azure Monitor för att analysera den med andra övervaknings data och spara data i mer än 90 dagar. Se [samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta i Azure Monitor](activity-log-collect.md).

![Fråga aktivitets logg](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportera aktivitetslogg
Exportera aktivitets loggen till Azure Storage för arkivering eller strömma den till en Händelsehubben för inmatning av en tjänst från tredje part eller en anpassad analys lösning. Se [Exportera Azure aktivitets loggen](activity-log-export.md). Du kan också analysera aktivitets logg händelser i Power BI med hjälp av [**Power BI innehålls paketet**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Avisering om aktivitets logg
Du kan skapa en avisering när särskilda händelser skapas i aktivitets loggen med en [aktivitets logg avisering](activity-log-alerts.md). Du kan också skapa en avisering med en [logg fråga](alerts-log-query.md) när aktivitets loggen är ansluten till en Log Analytics arbets yta, men det finns en kostnad för att logga frågor om aviseringar. Det finns ingen kostnad för aktivitets logg aviseringar.

## <a name="categories-in-the-activity-log"></a>Kategorier i aktivitets loggen
Varje händelse i aktivitets loggen har en viss kategori som beskrivs i följande tabell. Fullständig information om scheman av dessa kategorier finns i [Azure-aktivitetsloggen Händelseschema](activity-log-schema.md). 

| Category | Beskrivning |
|:---|:---|
| Administrativ | Innehåller posten över alla åtgärder för att skapa, uppdatera, ta bort och åtgärd som utförs via Resource Manager. Exempel på administrativa händelser är att _skapa en virtuell dator_ och _ta bort nätverks säkerhets grupp_.<br><br>Varje åtgärd som utförs av en användare eller ett program som använder Resource Manager är modellerad som en åtgärd på en viss resurs typ. Om åtgärds typen är _Skriv_, _ta bort_eller _åtgärd_registreras posterna för både start och lyckad eller misslyckad åtgärd i den administrativa kategorin. Administrativa händelser inkluderar även eventuella ändringar av rollbaserad åtkomst kontroll i en prenumeration. |
| Service Health | Innehåller posten för eventuella service Health-incidenter som har inträffat i Azure. Ett exempel på en Service Health händelse _SQL Azure i östra USA drabbas av drift stopp_. <br><br>Service Health händelser levereras på sex sorter: _Åtgärd krävs_, _stöd för återställning_, _incident_, _Underhåll_, _information_eller _säkerhet_. Dessa händelser skapas endast om du har en resurs i prenumerationen som skulle påverkas av händelsen.
| Resource Health | Innehåller posten för eventuella resurs hälso händelser som har inträffat på dina Azure-resurser. Ett exempel på en Resource Health-händelse är _hälso statusen för den virtuella datorn har ändrats till otillgänglig_.<br><br>Resource Health händelser kan representera en av fyra hälso status: _Tillgänglig, ej_ _tillgänglig_,degraderad och _okänd_. Dessutom kan Resource Health händelser kategoriseras som plattform som _initieras_ eller _användaren initieras_. |
| Varning | Innehåller posten för aktiveringar för Azure-aviseringar. Ett exempel på en varnings händelse är _CPU% på myVM har varit över 80 under de senaste 5 minuterna_.|
| Automatisk skalning | Innehåller posten för alla händelser som rör driften av autoskalning-motorn baserat på de inställningar för autoskalning som du har definierat i din prenumeration. Ett exempel på en autoskalning-händelse är autoskalning- _åtgärden misslyckades_. |
| Rekommendation | Innehåller rekommendations händelser från Azure Advisor. |
| Säkerhet | Innehåller posten för eventuella aviseringar som genererats av Azure Security Center. Ett exempel på en säkerhets händelse är _misstänkt dubbel tilläggs fil som körs_. |
| Princip | Innehåller poster med åtgärder som utförs av alla åtgärder som utförs av Azure Policy. Exempel på princip händelser är _granskning_ och _neka_. Varje åtgärd som utförs av principen är modellerad som en åtgärd på en resurs. |


## <a name="next-steps"></a>Nästa steg

* [Skapa en logg profil för att exportera Azure aktivitets loggen](activity-log-export.md)
* [Strömma Azure-aktivitets loggen till Event Hubs](activity-logs-stream-event-hubs.md)
* [Arkivera Azures aktivitets logg i lagrings utrymmet](archive-activity-log.md)

