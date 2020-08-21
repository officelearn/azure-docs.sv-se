---
title: Migrera till anslutnings övervakaren (förhands granskning) från anslutnings övervakaren
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till anslutnings övervakaren (förhands granskning) från anslutnings övervakaren.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701842"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrera till anslutnings övervakaren (förhands granskning) från anslutnings övervakaren

Du kan migrera befintliga anslutnings övervakare till den nya och förbättrade anslutnings övervakaren (för hands version) i ett klick och med noll stillestånds tid. Om du vill veta mer om fördelarna kan du läsa [anslutnings övervakaren (för hands version)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Viktiga punkter att Observera

* Agenter och brand Väggs inställningar fungerar som de är (ingen beröring krävs) 
* Befintliga anslutnings Övervakare kommer att mappas till anslutnings övervakaren (förhands granskning) – > test grupp – > test format. Användare kan klicka på *Redigera* för att visa och ändra egenskaperna för den nya anslutnings övervakaren och hämta mallen för att göra ändringar i anslutnings övervakaren och skicka den via Azure Resource Manager. 
* Azure Virtual Machines med Network Watcher-tillägget skicka data till både arbets ytan och måtten. Anslutnings övervakare gör data tillgängliga via de nya måtten (ChecksFailedPercent (för hands version) och RoundTripTimeMs (för hands version)) i stället för de gamla måtten stop (ProbesFailedPercent och AverageRoundtripMs) 
* Övervaka data
    * Aviseringar – kommer att migreras till nya mått som en del av migreringen
    * Instrument paneler och integreringar – du måste redigera mått uppsättningarna manuellt. 
    
## <a name="prerequisites"></a>Förutsättningar

Om du använder en anpassad arbets yta, se till att Network Watcher är aktiverat i prenumeration och region i arbets ytan Log Analytics 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Steg för att migrera från anslutnings övervakaren till anslutnings övervakaren (för hands version)

1. Klicka på "anslutnings övervakare" och navigera till "migrera anslutnings övervakare" för att migrera anslutnings övervakare från äldre till nyare lösning.

    ![Skärm bild som visar för hands versionen av migrera anslutningar till anslutnings övervakaren](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Välj prenumerations-och anslutnings övervakare och klicka på Migrera markerade. I ett klickar du på migrera befintliga anslutnings övervakare till anslutnings övervakaren (för hands version) 
1. Du kan anpassa egenskaperna för anslutnings övervakaren, ändra standard arbets yta, Hämta mall och kontrol lera migreringens status. 
1. När migreringen har påbörjats sker följande ändringar: 
    1. Azure Resource Manager resurs ändringar i den nyare anslutnings övervakaren
        1. Namn, region och prenumeration på anslutnings övervakaren förblir oförändrade. Därför påverkas inte resurs-ID: t.
        1. Om du inte anpassar skapas en standard arbets yta Log Analytics i regionen och prenumerationen på anslutnings övervakaren. Den här arbets ytan är platsen där övervaknings data ska lagras. Test resultat data kommer också att lagras i mått.
        1. Varje test migreras till en test grupp som kallas * defaultTestGroup *
        1.  Käll-och mål slut punkter skapas och används i den skapade test gruppen. Standard namn är *defaultSourceEndpoint* och *defaultDestinationEndpoint*
        1. Mål porten och ett söknings intervall flyttas till test konfigurationen som kallas *defaultTestConfiguration*. Protokollet ställs in baserat på port värden. Tröskelvärden för lyckade och andra valfria egenskaper lämnas tomma.
    1. Mått aviseringar migreras till anslutnings övervakaren (förhands granskning) mått varningar. Måtten är olika <link to metric section in the doc> , och därför ändras
    1. De migrerade anslutnings övervakarna visas inte i den äldre lösningen för anslutnings övervakning. de kommer nu bara att vara tillgängliga för användning i anslutnings övervakaren (förhands granskning)
    1. Alla externa integreringar som instrument paneler i Power BI, Grafana, integreringar med SIEM-system, måste migreras av användaren direkt. Det här är det enda manuella steget som användaren behöver utföra för att migrera sitt installations program.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du migrerar från övervakare av nätverksprestanda till anslutnings övervakaren (för hands version)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Lär dig [hur du skapar anslutnings övervakaren (för hands version) med Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
