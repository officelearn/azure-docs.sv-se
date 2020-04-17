---
title: Frågor om identifierings-, utvärderings- och beroendeanalys i Azure Migrate
description: Få svar på vanliga frågor om identifiering, utvärdering och beroendeanalys i Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 700e5318b66cdf4993a17b1dae85fb43f75ab035
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529781"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Upptäckt, bedömning och beroendeanalys - Vanliga frågor

Den här artikeln besvarar vanliga frågor om identifiering, utvärdering och beroendeanalys i Azure Migrate. Om du har andra frågor kontrollerar du dessa resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [servermigrering](common-questions-server-migration.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Vilka geografiska områden stöds för identifiering och utvärdering med Azure Migrate?

Granska de geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [statliga moln](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hur många virtuella datorer kan jag upptäcka med en apparat?

Du kan upptäcka upp till 10 000 virtuella virtuella datorer med VMware, upp till 5 000 virtuella virtuella datorer med hyper-V och upp till 250 fysiska servrar med hjälp av en enda apparat. Om du har fler datorer läser du om [skalning av en Hyper-V-bedömning,](scale-hyper-v-assessment.md) [skalning av en VMware-bedömning](scale-vmware-assessment.md)eller [skalning av en fysisk serverutvärdering](scale-physical-assessment.md).


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Storleken på min virtuella dator har ändrats. Kan jag göra en bedömning igen?

Azure Migrate-enheten samlar kontinuerligt in information om den lokala miljön.  En bedömning är en ögonblicksbild av lokala virtuella datorer. Om du ändrar inställningarna för en virtuell dator som du vill bedöma använder du alternativet beräkna om för att uppdatera utvärderingen med de senaste ändringarna.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hur upptäcker jag virtuella datorer i en miljö med flera skikt?

- **VMware:** Om en miljö delas mellan klienter och du inte vill identifiera en klients virtuella datorer i en annan klients prenumeration skapar du VMware vCenter Server-autentiseringsuppgifter som bara kan komma åt de virtuella datorer som du vill identifiera. Använd sedan dessa autentiseringsuppgifter när du startar identifieringen i Azure Migrate-installationen.
- **Hyper-V:** Identifiering använder Hyper-V-värdautentiseringsuppgifter. Om virtuella datorer delar samma Hyper-V-värd finns det för närvarande inget sätt att separera identifieringen.  

## <a name="do-i-need-vcenter-server"></a>Behöver jag vCenter Server?

Ja, Azure Migrate kräver vCenter Server i en VMware-miljö för att utföra identifiering. Azure Migrate stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter Server.

## <a name="what-are-the-sizing-options"></a>Vilka är storleksalternativen?

Med lokal storlek tar Azure Migrate inte hänsyn till VM-prestandadata för bedömning. Azure Migrate bedömer VM-storlekar baserat på den lokala konfigurationen. Med prestandabaserad storlek baseras storlek på användningsdata.

Om en lokal virtuell dator till exempel har fyra kärnor och 8 GB minne vid 50 % CPU-användning och 50 % minnesanvändning:
- Som lokal storlek rekommenderar en Azure VM SKU som har fyra kärnor och 8 GB minne.
- Prestandabaserad storleksändring rekommenderar en VM-SKU som har två kärnor och 4 GB minne eftersom utnyttjandeprocenten beaktas.

På samma sätt beror diskstorleken på storlekskriterier och lagringstyp:
- Om storlekskriterierna är prestandabaserade och lagringstypen är automatisk tar Azure Migrate hänsyn till diskens IOPS- och dataflödesvärden när den identifierar måldisktypen (Standard eller Premium).
- Om storlekskriterierna är prestandabaserade och lagringstypen är Premium rekommenderar Azure Migrate en Premium-disk SKU baserat på storleken på den lokala disken. Samma logik tillämpas på diskstorlek när storleksändringen är lokal och lagringstypen är Standard eller Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>Påverkar prestandahistorik och utnyttjande storlek?

Ja, prestandahistorik och användning påverkar storlekssorteringen i Azure Migrate.

### <a name="performance-history"></a>Prestandahistorik

Endast för prestandabaserad storlek samlar Azure Migrate prestandahistoriken för lokala datorer och använder den sedan för att rekommendera den virtuella datorns storlek och disktyp i Azure:

1. Apparaten profilerar kontinuerligt den lokala miljön för att samla in realtidsdata var 20:e sekund.
1. Apparaten rullar upp de insamlade 20-sekundersproverna och använder dem för att skapa en enda datapunkt var 15:e minut.
1. Om du vill skapa datapunkten väljer apparaten toppvärdet från alla 20 sekunders samplingar.
1. Installationen skickar datapunkten till Azure.

### <a name="utilization"></a>Utnyttjande

När du skapar en utvärdering i Azure, beroende på prestandavaraktighet och percentilvärdet för prestandahistorik som har angetts, beräknar Azure Migrate det effektiva utnyttjandevärdet och använder det sedan för storlek.

Om du till exempel anger prestandavaraktigheten till en dag och percentilvärdet till 95:e percentilen sorterar Azure Migrate de 15-minuters exempelpunkter som skickas av samlaren för den senaste dagen i stigande ordning. Den väljer det 95:e percentilvärdet som ett effektivt utnyttjande.

Om du använder det 95:e percentilvärdet ignoreras extremvärdena. Extremvärden kan inkluderas om din Azure Migrate använder den 99:e percentilen. Om du vill välja maximal användning för perioden utan att missa några extremvärden anger du att Azure Migrate ska använda den 99:e percentilen.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Hur skiljer sig importbaserade bedömningar från bedömningar med identifieringskälla som apparat?

Importbaserade utvärderingar är utvärderingar som skapats med datorer som importeras till Azure Migrate med hjälp av en CSV-fil. Endast fyra fält är obligatoriska att importera: Servernamn, kärnor, minne och operativsystem. Här är några saker att tänka på: 
 - Beredskapskriterierna är mindre stränga i importbaserade utvärderingar av parametern starttyp. Om starttypen inte anges antas det att datorn har BIOS-starttyp och att datorn inte är markerad som **Villkorligt klar**. I utvärderingar med identifieringskälla som installation markeras beredskapen som **Villkorligt klar** om starttypen saknas. Den här skillnaden i beredskapsberäkning beror på att användarna kanske inte har all information om datorerna i de tidiga stadierna av migreringsplanering när importbaserade utvärderingar görs. 
 - Prestandabaserade importutvärderingar använder det användningsvärde som tillhandahålls av användaren för beräkningar med rätt storlek. Eftersom användningsvärdet tillhandahålls av användaren inaktiveras alternativen **Prestandahistorik** och **Percentil-användning** i bedömningsegenskaperna. I bedömningar med identifieringskälla som apparat plockas det valda percentilvärdet från prestandadata som samlas in av apparaten.

## <a name="what-is-dependency-visualization"></a>Vad är beroendevisualisering?

Beroendevisualisering kan hjälpa dig att bedöma grupper av virtuella datorer att migrera med större förtroende. Beroendevisualisering dubbelkontrollerar datorberoenden innan du kör en utvärdering. Det hjälper till att säkerställa att ingenting lämnas kvar och det hjälper till att undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder servicemappningslösningen i Azure Monitor för att aktivera beroendevisualisering. [Läs mer](concepts-dependency-visualization.md).

> [!NOTE]
> Beroendevisualisering är inte tillgängligt i Azure Government.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Vad är skillnaden mellan agentbaserad och agentlös?

Skillnaderna mellan agentlös visualisering och agentbaserad visualisering sammanfattas i tabellen.

**Krav** | **Utan agent** | **Agent-baserade**
--- | --- | ---
Support | Det här alternativet är för närvarande i förhandsversion och är endast tillgängligt för virtuella datorer med VMware. [Granska](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) operativsystem som stöds. | I allmänhet tillgänglighet (GA).
Agent | Du behöver inte installera agenter på maskiner som du vill dubbelkolla. | Agenter som ska installeras på varje lokal dator som du vill analysera: [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)och [beroendeagenten](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Krav | [Granska](concepts-dependency-visualization.md#agentless-analysis) förutsättningarna och distributionskraven. | [Granska](concepts-dependency-visualization.md#agent-based-analysis) förutsättningarna och distributionskraven.
Log Analytics | Krävs inte. | Azure Migrate [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) använder servicemappningslösningen i [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) för beroendevisualisering. [Läs mer](concepts-dependency-visualization.md#agent-based-analysis).
Hur det fungerar | Samlar in TCP-anslutningsdata på datorer som är aktiverade för beroendevisualisering. Efter identifieringen samlar den in data med fem minuters mellanrum. | Service Map-agenter som är installerade på en dator samlar in data om TCP-processer och inkommande/utgående anslutningar för varje process.
Data | Källmaskinsservernamn, process, programnamn.<br/><br/> Målmaskinsserverns namn, process, programnamn och port. | Källmaskinsservernamn, process, programnamn.<br/><br/> Målmaskinsserverns namn, process, programnamn och port.<br/><br/> Antal anslutningar, svarstid och dataöverföringsinformation samlas in och är tillgängligt för Log Analytics-frågor. 
Visualisering | Beroendekarta över en server kan visas under en timme till 30 dagar. | Beroendekarta över en enda server.<br/><br/> Kartan kan bara visas under en timme.<br/><br/> Beroendekarta över en grupp servrar.<br/><br/> Lägg till och ta bort servrar i en grupp från kartvyn.
Dataexport | Det går för närvarande inte att hämta i tabellformat. | Data kan efterfrågas med Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Betalar jag för beroendevisualisering?

Nej. Läs mer om [Azure Migrate-priser](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Vad installerar jag för agentbaserad beroendevisualisering?

Om du vill använda agentbaserad beroendevisualisering hämtar och installerar du agenter på varje lokal dator som du vill utvärdera:

- [Microsoft-övervakningsagent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Beroendeagent](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Om du har datorer som inte har internetanslutning laddar du ned och installerar Log Analytics-gatewayen på dem.

Du behöver dessa agenter endast om du använder agentbaserad beroendevisualisering.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbetsyta?

Ja, för agentbaserad beroendevisualisering kan du koppla en befintlig arbetsyta till migreringsprojektet och använda den för beroendevisualisering. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera beroendevisualiseringsrapporten?

Nej, beroendevisualiseringsrapporten i agentbaserad visualisering kan inte exporteras. Azure Migrate använder dock Service Map och du kan använda [REST-API:et för tjänstmappning](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) för att hämta beroenden i JSON-format.

## <a name="can-i-automate-agent-installation"></a>Kan jag automatisera agentinstallationen?

För agentbaserad beroendevisualisering:

- Använd ett [skript för att installera beroendeagenten](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- För MMA [använder du kommandoraden eller automatiseringen](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)eller använder ett [skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Förutom skript kan du använda distributionsverktyg som Microsoft Endpoint Configuration Manager och [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) för att distribuera agenterna.

## <a name="what-operating-systems-does-mma-support"></a>Vilka operativsystem stöder MMA?

- Visa listan över [Windows-operativsystem som MMA stöder](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visa listan över [Linux-operativsystem som MMA stöder](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kan jag visualisera beroenden i mer än en timme?

För agentbaserad visualisering kan du visualisera beroenden i upp till en timme. Du kan gå tillbaka så långt som till ett visst datum i historiken, men den maximala tiden för visualisering är en timme. Du kan till exempel använda tidsvaraktigheten i beroendekartan för att visa beroenden för igår, men du kan bara visa beroenden för ett entimmesfönster. Du kan dock använda Azure Monitor-loggar för att [fråga beroendedata](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) under en längre tid.

För agentlös visualisering kan du visa beroendekartan för en enda server från en varaktighet på mellan en timme och 30 dagar.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kan jag visualisera beroenden för grupper med fler än 10 virtuella datorer?

Du kan [visualisera beroenden](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) för grupper som har upp till 10 virtuella datorer. Om du har en grupp som har fler än 10 virtuella datorer rekommenderar vi att du delar upp gruppen i mindre grupper och sedan visualiserar beroenden.

## <a name="next-steps"></a>Nästa steg

Läs [översikten över Azure Migrate](migrate-services-overview.md).
