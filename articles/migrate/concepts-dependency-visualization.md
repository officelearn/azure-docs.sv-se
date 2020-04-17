---
title: Beroendeanalys i Utvärdering av Azure Migrate Server
description: Beskriver hur du använder beroendeanalys för utvärdering med Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b11796f2c5d7c1d87f383e6780444e572352eff5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537754"
---
# <a name="dependency-analysis"></a>Beroendeanalys

I den här artikeln beskrivs beroendeanalys i Azure Migrate:Server Assessment.

## <a name="overview"></a>Översikt

Beroendeanalys hjälper dig att identifiera beroenden mellan lokala datorer som du vill bedöma och migrera till Azure. 

- I Azure Migrate:Server Assessment samlar du in datorer i en grupp och utvärderar sedan gruppen. Beroendeanalys hjälper dig att gruppera datorer mer exakt, med högt förtroende för bedömning.
- Med beroendeanalys kan du identifiera datorer som måste migreras tillsammans. Du kan identifiera om datorer används eller om de kan inaktiveras i stället för migreras.
- Genom att analysera beroenden säkerställs att ingenting lämnas kvar och undvika överraskande avbrott under migreringen.
- Analys är särskilt användbart om du inte är säker på om datorer är en del av en appdistribution som du vill migrera till Azure.
- [Granska](common-questions-discovery-assessment.md#what-is-dependency-visualization) vanliga frågor om beroendeanalys.

Det finns två alternativ för att distribuera beroendeanalys

- **Agentbaserad**: Agentbaserad beroendeanalys kräver att agenter installeras på varje lokal dator som du vill analysera.
- **Agentless**: Med agentless analys, behöver du inte installera agenter på maskiner som du vill dubbelkolla. Det här alternativet är för närvarande i förhandsversion och är endast tillgängligt för virtuella datorer med VMware.

> [!NOTE]
> Agentbaserad beroendeanalys är inte tillgänglig i Azure Government. Du kan använda agentless dependency analysis.

## <a name="agentless-analysis"></a>Agentlös analys

Agentless dependency analysis works by capturing TCP connection data from machines which it's enabled. Inga agenter är installerade på datorer som du vill analysera.

### <a name="collected-data"></a>Insamlade data

När beroendeidentifieringen har startat avser enheten data från datorer var femte minut för att samla in data. Dessa data samlas in från gäst-virtuella datorer via vCenter Server med hjälp av virtuella API:er för sfären. De insamlade data bearbetas på Azure Migrate-enheten, för att härleda identitetsinformation och skickas till Azure Migrate var sjätte timme.

Polling samlar in dessa data från maskiner: 
- Namn på processer som har aktiva anslutningar.
- Namn på program som kör processer som har aktiva anslutningar.
- Målporten på de aktiva anslutningarna.

## <a name="agent-based-analysis"></a>Agentbaserad analys

För agentbaserad analys använder Server Assessment [service map-lösningen](../azure-monitor/insights/service-map.md) i Azure Monitor för att aktivera beroendevisualisering och analys. [Microsoft Monitoring Agent/Log Analytics-agenten](../azure-monitor/platform/agents-overview.md#log-analytics-agent) och [beroendeagenten](../azure-monitor/platform/agents-overview.md#dependency-agent)måste installeras på varje dator som du vill analysera.

### <a name="collected-data"></a>Insamlade data

För agentbaserad visualisering samlas följande data in:

- Källmaskinsservernamn, process, programnamn.
- Målmaskinsserverns namn, process, programnamn och port.
- Antal anslutningar, svarstid och dataöverföringsinformation samlas in och är tillgängligt för Log Analytics-frågor. 


## <a name="compare-agentless-and-agent-based"></a>Jämför agentless och agent-baserade

Skillnaderna mellan agentlös visualisering och agentbaserad visualisering sammanfattas i tabellen.

**Krav** | **Utan agent** | **Agent-baserade**
--- | --- | ---
Support | Det här alternativet är för närvarande i förhandsversion och är endast tillgängligt för virtuella datorer med VMware. [Granska](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) operativsystem som stöds. | I allmänhet tillgänglighet (GA).
Agent | Du behöver inte installera agenter på maskiner som du vill dubbelkolla. | Agenter som ska installeras på varje lokal dator som du vill analysera: [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)och [beroendeagenten](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Krävs inte. | Azure Migrate använder [servicemappningslösningen](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) i [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) för beroendeanalys. 
Hur det fungerar | Samlar in TCP-anslutningsdata på datorer som är aktiverade för beroendevisualisering. Efter identifieringen samlar den in data med fem minuters mellanrum. | Service Map-agenter som är installerade på en dator samlar in data om TCP-processer och inkommande/utgående anslutningar för varje process.
Data | Källmaskinsservernamn, process, programnamn.<br/><br/> Målmaskinsserverns namn, process, programnamn och port. | Källmaskinsservernamn, process, programnamn.<br/><br/> Målmaskinsserverns namn, process, programnamn och port.<br/><br/> Antal anslutningar, svarstid och dataöverföringsinformation samlas in och är tillgängligt för Log Analytics-frågor. 
Visualisering | Beroendekarta över en server kan visas under en timme till 30 dagar. | Beroendekarta över en enda server.<br/><br/> Kartan kan bara visas under en timme.<br/><br/> Beroendekarta över en grupp servrar.<br/><br/> Lägg till och ta bort servrar i en grupp från kartvyn.
Dataexport | Det går för närvarande inte att hämta i tabellformat. | Data kan efterfrågas med Log Analytics.



## <a name="next-steps"></a>Nästa steg
- Granska kraven för att ställa in agentbaserad analys för [virtuella datorer med VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [fysiska servrar](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)och [virtuella hyper-virtuella datorer](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Granska](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) kraven för agentless analys av virtuella datorer.
- [Ställ in](how-to-create-group-machine-dependencies.md) agentbaserad beroendevisualisering
- [Prova](how-to-create-group-machine-dependencies-agentless.md) agentless beroende visualisering för virtuella datorer med VMware.
- Granska [vanliga frågor](common-questions-discovery-assessment.md#what-is-dependency-visualization) om beroendevisualisering.


