---
title: Beroende analys i Azure Migrate Server-utvärdering
description: Beskriver hur du använder beroende analys för utvärdering med Azure Migrate Server bedömning.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024769"
---
# <a name="dependency-analysis"></a>Beroende analys

I den här artikeln beskrivs beroende analys i Azure Migrate: Server utvärdering.

## <a name="overview"></a>Översikt

Beroende analys hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. 

- I Azure Migrate: Server utvärdering, samla in datorer i en grupp och sedan utvärdera gruppen. Med hjälp av beroende analys kan du gruppera datorer mer noggrant, med hög exakthet för utvärdering.
- Med beroende analys kan du identifiera datorer som måste migreras tillsammans. Du kan identifiera om datorer används eller om de kan tas ur bruk i stället för att migreras.
- Analys av beroenden hjälper till att se till att ingenting är kvar och Undvik oväntade avbrott under migreringen.
- Analys är särskilt användbart om du inte är säker på om datorerna är en del av en app-distribution som du vill migrera till Azure.
- [Läs](common-questions-discovery-assessment.md#what-is-dependency-visualization) vanliga frågor om beroende analyser.

Det finns två alternativ för att distribuera beroende analyser

- **Agent-baserad**: agent-baserad beroende analys kräver att agenter installeras på varje lokal dator som du vill analysera.
- Utan **agent**: med en agent lös analys behöver du inte installera agenter på datorer som du vill kryssa för. Det här alternativet är för närvarande en för hands version och är bara tillgängligt för virtuella VMware-datorer.

> [!NOTE]
> Agent-baserad beroende analys är inte tillgänglig i Azure Government. Du kan använda agentens beroende analys.

## <a name="agentless-analysis"></a>Analys utan agent

Analytiska beroende analyser fungerar genom att samla in TCP-anslutningsfel från datorer som den är aktive rad för. Inga agenter är installerade på datorer som du vill analysera.

### <a name="collected-data"></a>Insamlade data

När beroende identifiering startar avsöker enheten data från datorer var femte minut för att samla in data. Dessa data samlas in från virtuella gäst datorer via vCenter Server med hjälp av vSphere-API: er. Insamlade data bearbetas på Azure Migrates enheten, för att härleda identitets information och skickas till Azure Migrate var sjätte timme.

Avsökningen samlar in dessa data från datorer: 
- Namn på processer som har aktiva anslutningar.
- Namnet på programmet som kör processer som har aktiva anslutningar.
- Målport på aktiva anslutningar.

## <a name="agent-based-analysis"></a>Agent-baserad analys

För agentbaserade analyser använder Server utvärderingen [tjänstkarta lösning](../azure-monitor/insights/service-map.md) i Azure Monitor för att aktivera beroende visualisering och analys. [Microsoft Monitoring Agent/Log Analytics-agenten](../azure-monitor/platform/agents-overview.md#log-analytics-agent) och [beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent)måste installeras på varje dator som du vill analysera.

### <a name="collected-data"></a>Insamlade data

För en agent-baserad analys samlas följande data in:

- Käll datorns Server namn, process, program namn.
- Mål datorns Server namn, process, program namn och port.
- Antalet anslutningar, svars tid och data överförings information samlas in och är tillgängliga för Log Analytics frågor. 


## <a name="compare-agentless-and-agent-based"></a>Jämför agent utan agent och agent-baserad

Skillnaderna mellan agent utan visualisering och agentbaserade visualiseringar sammanfattas i tabellen.

**Krav** | **Utan agent** | **Agent-baserad**
--- | --- | ---
Support | Det här alternativet är för närvarande en för hands version och är bara tillgängligt för virtuella VMware-datorer. [Granska](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) operativ system som stöds. | Allmän tillgänglighet (GA).
Agent | Du behöver inte installera agenter på datorer som du vill kryssa för. | Agenter som ska installeras på varje lokal dator som du vill analysera: [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)och [beroende agenten](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Krävs inte. | Azure Migrate använder [tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) -lösningen i [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) för beroende analys. 
Så här fungerar det | Fångar upp TCP-anslutningsfel på datorer aktiverade för beroende visualisering. Efter identifieringen samlar den in data i intervall om fem minuter. | Tjänstkarta agenter som installerats på en dator samla in data om TCP-processer och inkommande/utgående anslutningar för varje process.
Data | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port. | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port.<br/><br/> Antalet anslutningar, svars tid och data överförings information samlas in och är tillgängliga för Log Analytics frågor. 
Visualisering | Beroende karta för enskild server kan visas över en varaktighet på en timme till 30 dagar. | Beroende karta för en enskild server.<br/><br/> Kartan kan endast visas över en timme.<br/><br/> Beroende karta för en grupp med servrar.<br/><br/> Lägga till och ta bort servrar i en grupp från MAP-vyn.
Dataexport | Kan för närvarande inte hämtas i tabell format. | Data kan frågas med Log Analytics.



## <a name="next-steps"></a>Nästa steg
- Granska kraven för att ställa in en agent-baserad analys för virtuella [VMware-datorer](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [fysiska servrar](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)och [virtuella Hyper-V-datorer](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Granska](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) kraven för agent analys av virtuella VMware-datorer.
- [Konfigurera](how-to-create-group-machine-dependencies.md) en agent baserad beroende visualisering
- [Testa övervakning utan](how-to-create-group-machine-dependencies-agentless.md) agent beroende för virtuella VMware-datorer.
- Läs [vanliga frågor](common-questions-discovery-assessment.md#what-is-dependency-visualization) om beroende visualisering.


