---
title: Stöd för fysisk serverutvärdering i Azure Migrate
description: Lär dig mer om stöd för fysisk serverutvärdering med Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538162"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Stödmatris för fysisk serverbedömning 

Den här artikeln sammanfattar krav på förutsättningar och support när du bedömer fysiska servrar för migrering till Azure med hjälp av verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Om du vill migrera fysiska servrar till Azure läser du [distributionsstödmatrisen](migrate-support-matrix-physical-migration.md).


Om du vill bedöma fysiska servrar skapar du ett Azure Migrate-projekt och lägger till serverbedömningsverktyget i projektet. När verktyget har lagts till distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Installationen identifierar kontinuerligt lokala datorer och skickar datorns metadata och prestandadata till Azure. När identifieringen är klar samlar du identifierade datorer i grupper och kör en utvärdering för en grupp.


## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Bedömningsgränser** | Du kan identifiera och bedöma upp till 35 000 fysiska servrar i ett enda [Azure Migrate-projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projektbegränsningar** | Du kan skapa flera projekt i en Azure-prenumeration. Förutom fysiska servrar kan ett projekt innehålla virtuella datorer med VMware och virtuella datorer med hyper-vm, upp till bedömningsgränserna för varje.
**Identifiering** | Azure Migrate-enheten kan identifiera upp till 250 fysiska servrar.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan bedöma upp till 35 000 maskiner i en enda bedömning.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="physical-server-requirements"></a>Krav för fysisk server

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distribuera fysisk server**       | Den fysiska servern kan vara fristående eller distribueras i ett kluster. |
| **Behörigheter**           | **Windows:** Du behöver ett lokalt användarkonto eller domänanvändarkonto på alla Windows-servrar som du vill identifiera. Användarkontot ska läggas till i dessa grupper: Användare av fjärrskrivbord, användare av prestandaövervakaren och prestandalogganvändare. <br/><br/> **Linux:** Du behöver ett rotkonto på De Linux-servrar som du vill upptäcka. |
| **Operativsystem** | Alla [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux-operativsystem](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) som stöds av Azure, förutom Windows Server 2003 och SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrate-enheten](migrate-appliance.md) för identifiering och utvärdering. Apparaten för fysiska servrar kan köras på en virtuell dator eller en fysisk dator. Du ställer in installationen med ett PowerShell-skript som du hämtar från Azure-portalen.

- Läs mer om [installationskrav](migrate-appliance.md#appliance---physical) för fysiska servrar.
- Läs mer om webbadresser som apparaten behöver komma åt i [offentliga](migrate-appliance.md#public-cloud-urls) moln och [myndighetsmoln.](migrate-appliance.md#government-cloud-urls)

## <a name="port-access"></a>Tillträde till port

I följande tabell sammanfattas portkrav för bedömning.

**Enhet** | **Anslutning**
--- | ---
**Apparaten** | Inkommande anslutningar på TCP-port 3389, för att tillåta fjärrskrivbordsanslutningar till apparaten.<br/><br/> Inkommande anslutningar på port 44368 för att fjärransluta till appen för hantering av apparater med hjälp av URL:en:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Utgående anslutningar på portar 443 (HTTPS), för att skicka identifierings- och prestandametadata till Azure Migrate.
**Fysiska servrar** | **Windows:** Inkommande anslutningar på WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations- och prestandametadata från Windows-servrar. <br/><br/> **Linux:**  Inkommande anslutningar på port 22 (UDP), för att hämta konfigurations- och prestandametadata från Linux-servrar. |

## <a name="agent-based-dependency-analysis-requirements"></a>Agentbaserade krav på beroendeanalys

[Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill bedöma och migrera till Azure. Tabellen sammanfattar kraven för att ställa in agentbaserad beroendeanalys. För närvarande stöds endast agentbaserad beroendeanalys för fysiska servrar.

**Krav** | **Detaljer** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med serverbedömningsverktyget tillagt i projektet.<br/><br/>  Du distribuerar beroendevisualisering efter att ha konfigurerat en Azure Migrate-installation för att identifiera dina lokala datorer<br/><br/> [Läs om hur du](create-manage-projects.md) skapar ett projekt för första gången.<br/> [Läs om hur du](how-to-assess.md) lägger till ett bedömningsverktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för bedömning av [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Azure Government** | Beroendevisualisering är inte tillgängligt i Azure Government.
**Log Analytics** | Azure Migrate [Service Map](../operations-management-suite/operations-management-suite-service-map.md) använder servicemappningslösningen i [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) för beroendevisualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics-arbetsyta med ett Azure Migrate-projekt. Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till. <br/><br/> Arbetsytan måste finnas i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbetsytan måste finnas i regionerna Östra USA, Sydostasien eller Västeuropa. Arbetsytor i andra regioner kan inte associeras med ett projekt.<br/><br/> Arbetsytan måste finnas i en region där [Service Map stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> I Logganalys taggas arbetsytan som är associerad med Azure Migrate med nyckeln Migreringsprojekt och projektnamnet.
**Nödvändiga agenter** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Beroendeagenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till internet måste du hämta och installera Log Analytics-gatewayen på dem.<br/><br/> Läs mer om hur du installerar [beroendeagenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-arbetsyta** | Arbetsytan måste finnas i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbetsytor som finns i regionerna Östra USA, Sydostasien och Västeuropa.<br/><br/>  Arbetsytan måste finnas i en region där [Service Map stöds](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till.
**Kostnader** | Service Map-lösningen medför inga avgifter för de första 180 dagarna (från den dag då du associerar Log Analytics-arbetsytan med Azure Migrate-projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Service Map på den associerade log analytics-arbetsytan medför [standardavgifter](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort tas arbetsytan inte bort tillsammans med den. När du har tagit bort projektet är användning av tjänstkartning inte gratis och varje nod debiteras enligt den betalda nivån på Log Analytics-arbetsytan/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA- 28 februari 2018), kan du ha ådragit dig ytterligare servicemappningsavgifter. För att säkerställa betalning efter endast 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbetsytor innan GA fortfarande är avgiftsbelagda.
**Hantering** | När du registrerar agenter på arbetsytan använder du ID och nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda log analytics-arbetsytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbetsytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbetsytan som skapats av Azure Migrate, såvida du inte tar bort Azure Migrate-projektet. Om du gör det kommer beroendevisualiseringsfunktionen inte att fungera som förväntat.
**Internetanslutning** | Om datorer inte är anslutna till internet måste du installera log analytics-gatewayen på dem.
**Azure Government** | Agentbaserad beroendeanalys stöds inte.

## <a name="next-steps"></a>Nästa steg

[Förbered dig för fysisk serverbedömning](tutorial-prepare-physical.md).
