---
title: Vanliga frågor – identifiering, utvärdering och beroende analys i Azure Migrate
description: Få svar på vanliga frågor om identifiering, utvärdering och beroende analys i Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7edc73742b61e4e5e94431c50d14d263bbbea641
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362254"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Vanliga frågor om identifiering, utvärdering och beroende analys

I den här artikeln besvaras vanliga frågor om identifiering, utvärdering och beroende analys i Azure Migrate. Om du har andra frågor kan du läsa följande artiklar:

- [Allmänna frågor](resources-faq.md) om Azure Migrate.
- [Frågor](common-questions-appliance.md) om Azure Migrate-enheten.
- [Frågor](common-questions-server-migration.md) om Server migrering.
- Publicera frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hur många virtuella datorer kan jag identifiera med en apparat?

Du kan identifiera upp till 10 000 virtuella VMware-datorer, upp till 5 000 virtuella Hyper-V-datorer och upp till 250 fysiska servrar med en enda apparat. Om du har fler datorer kan du läsa artiklarna om skalning av [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md)och [fysisk server](scale-physical-assessment.md) -utvärdering.



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>Storleken på den virtuella datorn har ändrats. Kan jag köra en utvärdering igen?

Azure Migrates enheten samlar kontinuerligt in information om lokala datorer och en utvärdering är en tidpunkts ögonblicks bild. Om du ändrar inställningarna för en virtuell dator som du vill utvärdera använder du alternativet beräkna om du vill uppdatera utvärderingen med de senaste ändringarna.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hur gör jag för att identifiera virtuella datorer i en miljö med flera organisationer?

- **VMware**: om en miljö delas mellan klienter och du inte vill identifiera en innehavares virtuella datorer i en annan klient prenumeration, skapar du vCenter Server autentiseringsuppgifter som bara kan komma åt de virtuella datorer som du vill identifiera. Använd sedan autentiseringsuppgifterna när du startar identifiering i Azure Migrate-installationen.
- **Hyper-v**: identifiering använder autentiseringsuppgifter för Hyper-v-värden. Om virtuella datorer delar samma Hyper-V-värd finns det för närvarande inget sätt att separera identifieringen.  


## <a name="do-i-need-vcenter-server"></a>Behöver jag vCenter Server?

Ja, Azure Migrate behöver vCenter Server för att utföra identifiering i en VMware-miljö. Den stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter Server.


## <a name="whats-are-the-sizing-options"></a>Vad är storleks alternativen?

Vid samma storlek som lokal storlek kan Azure Migrate inte beakta prestanda data för virtuella datorer för utvärdering. Den utvärderar VM-storlekar baserat på den lokala konfigurationen. Med prestanda-baserad storleks ändring baseras storleken på användnings data.

- Till exempel om en lokal virtuell dator har 4 kärnor och 8 GB minne med 50% processor användning och 50% minnes användning:
    - Som lokal storlek rekommenderar vi en Azure VM-SKU som har fyra kärnor och 8 GB minne.
    - Prestandabaserade storleks ändringar kommer att rekommendera en VM-SKU som har två kärnor och 4 GB minne, eftersom användnings procenten beaktas.

- På samma sätt beror disk storleken på storleks kriterierna och lagrings typen.
    - Om storleks kriteriet är prestanda beroende och lagrings typen är automatisk, använder Azure Migrate IOPS-och data flödes värden för disken i kontot när den identifierar mål disk typ (standard eller Premium).
    - Om storleks kriteriet är prestanda beroende och lagrings typen är Premium rekommenderar Azure Migrate en Premium-disk-SKU, baserat på den lokala diskens storlek. Samma logik används för disk storlek, när storleken är lokalt och lagrings typen är standard eller Premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>Påverkar prestanda historiken/användningen storleken?

Dessa egenskaper gäller endast för prestandabaserad storleksändring.

- Azure Migrate samlar in prestanda historiken för lokala datorer och använder den för att rekommendera den virtuella dator storleken och disk typen i Azure.
- Enheten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund.
- Enheten samlar in de 20 sekunderna exemplen och skapar en enda data punkt var 15: e minut.
- För att skapa data punkten väljer installationen det högsta värdet från alla exempel på 20 sekunder.
- Enheten skickar data punkten till Azure.

När du skapar en utvärdering i Azure, baserat på varaktigheten prestanda och prestanda historik, beräknar Azure Migrate det effektiva användning svärdet och använder det för storleks ändring.

- Om du till exempel ställer in varaktigheten för varaktigheten på en dag och percentilvärdet till 95 percentilen, sorterar Azure Migrate de 15 minuters exempel punkter som skickats av insamlaren under den senaste dagen i stigande ordning, och plockar värdet 95 percentilvärdet som effektiv användning.
- Genom att använda 95 percentilvärdet ser du till att avvikande värden ignoreras. Avvikare kan inkluderas om du använder 99-percentilen. Om du vill välja högsta användning för perioden, utan saknade avvikande, väljer du 99 percentilen.

## <a name="what-is-dependency-visualization"></a>Vad är beroende visualisering?

Använd beroende visualisering för att utvärdera grupper av virtuella datorer för migrering med större säkerhet. Beroende visualisering mellan kontroller av dator beroenden innan du kör en utvärdering. Det hjälper till att se till att inget är kvar bakom, och bidrar därmed till att undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder Tjänstkarta-lösningen i Azure Monitor för att aktivera beroende visualisering. [Läs mer](concepts-dependency-visualization.md).

> [!NOTE]
> Beroende visualisering är inte tillgänglig i Azure Government.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Vad är skillnaden mellan agent-och agenten?

Skillnaderna mellan agent utan visualisering och agentbaserade visualiseringar sammanfattas i tabellen.

**Krav** | **Utan agent** | **Agent-baserad**
--- | --- | ---
Support | Det här alternativet är för närvarande en för hands version och är bara tillgängligt för virtuella VMware-datorer. [Granska](migrate-support-matrix-vmware.md#agentless-dependency-visualization) operativ system som stöds. | Allmän tillgänglighet (GA).
Agent | Du behöver inte installera agenter på datorer som du vill kryssa för. | Agenter som ska installeras på varje lokal dator som du vill analysera: [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)och [beroende agenten](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Förutsättningar | [Granska](concepts-dependency-visualization.md#agentless-visualization) kraven och distributions kraven. | [Granska](concepts-dependency-visualization.md#agent-based-visualization) kraven och distributions kraven.
Log Analytics | Krävs inte. | Azure Migrate använder [tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) -lösningen i [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) för beroende visualisering. [Läs mer](concepts-dependency-visualization.md#agent-based-visualization).
Hur det fungerar | Fångar upp TCP-anslutningsfel på datorer aktiverade för beroende visualisering. Efter identifieringen samlar den in data i intervall om fem minuter. | Tjänstkarta agenter som installerats på en dator samla in data om TCP-processer och inkommande/utgående anslutningar för varje process.
Data | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port. | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port.<br/><br/> Antalet anslutningar, svars tid och data överförings information samlas in och är tillgängliga för Log Analytics frågor. 
Visualisering | Beroende karta för enskild server kan visas över en varaktighet på en timme till 30 dagar. | Beroende karta för en enskild server.<br/><br/> Kartan kan endast visas över en timme.<br/><br/> Beroende karta för en grupp med servrar.<br/><br/> Lägga till och ta bort servrar i en grupp från MAP-vyn.
Data export | Kan för närvarande inte hämtas i tabell format. | Data kan frågas med Log Analytics.




## <a name="do-i-pay-for-dependency-visualization"></a>Betalar jag för beroende visualisering?
Nej. [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Vad installerar jag för en agent-baserad beroende visualisering?

Om du vill använda agent-baserad beroende visualisering måste du ladda ned och installera agenter på varje lokal dator som du vill utvärdera.

Installera följande agenter på varje dator:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Beroende agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Om du har datorer som saknar Internet anslutning måste du ladda ned och installera Log Analytics gateway på dem.

Du behöver inte dessa agenter om du inte använder agent-baserad beroende visualisering.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbets yta?

Ja, för en agent-baserad beroende visualisering kan du koppla en befintlig arbets yta till migreringsjobbet och använda den för beroende visualisering. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera rapporten för beroende visualisering?

Nej, det går inte att exportera den beroende visualiserings rapporten i agent-baserad visualisering. Azure Migrate använder dock Tjänstkarta och du kan använda [tjänstkarta-REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) för att hämta beroenden i JSON-format.

## <a name="can-i-automate-agent-installation"></a>Kan jag automatisera Agent installationen?
För en agent-baserad beroende visualisering automatiserar du enligt följande:

- Använd det här [skriptet för att installera beroende agenten](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- För MMA följer du de här [instruktionerna](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) för att använda kommando raden eller Automation, eller Använd [det här skriptet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Förutom skript kan du också använda distributions verktyg som Microsoft Endpoint Configuration Manager och [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) för att distribuera agenterna.


## <a name="what-operating-systems-does-mma-support"></a>Vilka operativ system stöder MMA?

- Visa listan över [Windows-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visa listan över [Linux-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Kan jag visualisera beroenden i mer än en timme?
För en agent-baserad visualisering kan du visualisera beroenden i upp till en timme. Du kan gå tillbaka till ett visst datum i historiken, så långt tillbaka som en månad, men den maximala varaktigheten för visualiseringen är en timme. Du kan till exempel använda tids längden i beroende kartan för att Visa beroenden för igår, men du kan bara Visa beroenden för ett enda timme-fönster. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre tid.

För övervakning utan agent kan du Visa beroende kartan för en enskild server från en varaktighet på en timme till 30 dagar.


## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kan visualisera beroenden för grupper med fler än 10 virtuella datorer?
Du kan [visualisera beroenden](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) för grupper som innehåller upp till 10 virtuella datorer. Om du har en grupp med fler än 10 virtuella datorer rekommenderar vi att du delar upp gruppen i mindre grupper och sedan visualiserar beroendena.




## <a name="next-steps"></a>Nästa steg
Läs [Azure Migrate översikt](migrate-services-overview.md).
