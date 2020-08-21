---
title: Migrera till anslutnings övervakaren (förhands granskning) från Övervakare av nätverksprestanda
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till anslutnings övervakaren (för hands version) från Övervakare av nätverksprestanda.
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701833"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrera till anslutnings övervakaren (förhands granskning) från Övervakare av nätverksprestanda

Du kan migrera tester från Övervakare av nätverksprestanda till den nya och förbättrade anslutnings övervakaren (för hands version) i ett klick och med noll stillestånds tid. Om du vill veta mer om fördelarna kan du läsa [anslutnings övervakaren (för hands version)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> Endast tester från övervakaren för tjänst anslutning kan migreras till anslutnings övervakaren (för hands version).
>

## <a name="key-points-to-note"></a>Viktiga punkter att Observera

* Lokala agenter och brand Väggs inställningar fungerar som de är. Inga ändringar krävs. Log Analytics agenter som är installerade på Azure Virtual Machines måste ersättas med Network Watcher tillägg
* Befintliga tester mappas till anslutnings övervakaren (för hands version) – > test grupp – > test format. Användare kan klicka på *Redigera* för att visa och ändra egenskaperna för den nya anslutnings övervakaren och hämta mallen för att göra ändringar i anslutnings övervakaren och skicka den via Azure Resource Manager.
* Agenter skickar data till både – Log Analytics arbets yta och mått.
* Övervaka data
    * Data i Log Analytics – all data för migreringen fortsätter att finnas i arbets ytan där NPM har kon figurer ATS i NetworkMonitoring-tabellen. Efter migreringen går data till NetworkMonitoring-tabellen och ConnectionMonitor_CL tabellen i samma arbets yta. När testerna har inaktiverats från NPM kommer data endast att lagras i ConnectionMonitor_CL tabell
    * Logga baserade aviseringar, instrument paneler och integreringar – du måste redigera frågorna manuellt baserat på den nya tabellen ConnectionMonitor_CL. Du kan också återskapa aviseringarna i mått med hjälp av den här länken. Möjlighet att migrera loggar baserade aviseringar i NetworkMonitoring-tabellen till måttbaserade aviseringar automatiskt som en del av migreringen kommer snart att vara tillgänglig
    
## <a name="prerequisites"></a>Förutsättningar

*   Se till att Network Watcher är aktive rad i prenumeration och region på arbets ytan Log Analytics
*   Virtuella Azure-datorer med Log Analytics-agenter installerade måste aktive ras med Network Watcher tillägget

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Steg för att migrera tester från Övervakare av nätverksprestanda till anslutnings övervakaren (för hands version)

1.  Klicka på "anslutnings övervakare" och navigera till "migrera tester från NPM" för att migrera tester till anslutnings övervakaren (för hands version)

    ![Skärm bild som visar migrera tester från NPM till för hands version av anslutnings övervakaren](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Välj prenumeration, arbets yta och den NPM-funktion som du vill migrera. För närvarande kan du endast migrera tester från övervakaren för tjänst anslutning.  
1.  Klicka på Importera för att migrera test
1.  När migreringen har påbörjats sker följande ändringar: 
    1. En ny anslutnings övervaknings resurs skapas
        1. En anslutnings övervakare per region och prenumeration skapas. För tester med lokala agenter har namnet på den nya anslutnings övervakaren formatet <workspaceName> _"lokalt". För tester med Azure-agenter är det nya namnet på anslutnings övervakaren av formatet <workspaceName> _<Azure_region_name>
        1. Övervaknings data lagras nu i samma Log Analytics arbets yta där NPM är aktiverat, i en ny tabell som kallas Connectionmonitor_CL tabell. 
        1. Test namnet överförs till test gruppens namn. Test beskrivningen kommer inte att migreras.
        1. Käll-och mål slut punkter skapas och används i den skapade test gruppen. För lokala agenter namnges slut punkterna i formatet <workspaceName> _"slut punkt"_ <FQDN of on-premises machine> . För Azure måste du aktivera agenterna och migrera igen om de migrerande testerna innehåller agenter inte körs.
        1. Mål porten och söknings intervallet flyttas till test konfigurationen, nämligen "TC"_ <testname> "och" TC "_ <testname> _" AppThresholds ". Protokollet ställs in baserat på port värden. Tröskelvärden för lyckade och andra valfria egenskaper lämnas tomma.
    1. NPM har inte inaktiverats. Migrerade tester fortsätter därför att skicka data till NetworkMonitoring-tabellen samt ConnectionMonitor_CL tabell. Det här steget säkerställer att befintliga loggbaserade aviseringar och integreringar inte påverkas. Att migrera loggbaserade aviseringar i NetworkMonitoring-tabellen till måttbaserade aviseringar automatiskt som en del av migreringen blir snart tillgänglig.
    1. Den nyligen skapade anslutnings övervakaren visas i anslutnings övervakaren (förhands granskning)
1.  Efter migreringen måste du inaktivera testerna manuellt i NPM. Tills du gör det kan du fortsätta att debiteras för samma. När du inaktiverar NPM bör du se till att du återskapar aviseringarna på ConnectionMonitor_CL tabell eller använder mått. Se också till att alla externa integreringar som instrument paneler i Power BI, Grafana, integreringar med SIEM-system, måste migreras till ConnectionMonitor_CL-tabellen


## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du migrerar från anslutnings övervakaren till anslutnings övervakaren (för hands version)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Lär dig [hur du skapar anslutnings övervakaren (för hands version) med Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
