---
title: Beroendevisualisering i Azure Migrate
description: Innehåller en översikt över bedömnings beräkningar i Server Assessment service i Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: fd069ed98fa34fd6f281c98a061925f96c7bb2cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269710"
---
# <a name="dependency-visualization"></a>Visualisering av beroenden

I den här artikeln beskrivs beroende visualisering i Azure Migrate: Server utvärdering.

## <a name="what-is-dependency-visualization"></a>Vad är beroende visualisering?

Beroende visualisering hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. 

- I Azure Migrate: Server utvärdering, samla in datorer i en grupp och sedan utvärdera gruppen. Beroende visualisering gör att du kan gruppera datorer mer noggrant, med hög exakthet för utvärdering.
- Med beroende visualisering kan du identifiera datorer som måste migreras tillsammans. Du kan identifiera om datorer används eller om de kan tas ur bruk i stället för att migreras.
- Visualisering av beroenden hjälper till att se till att ingenting är kvar bakom och Undvik oväntade avbrott under migreringen.
- Visualisering är särskilt användbart om du inte är säker på om datorerna är en del av en app-distribution som du vill migrera till Azure.


> [!NOTE]
> Beroende visualisering är inte tillgänglig i Azure Government.

## <a name="agent-basedagentless-visualization"></a>Agent-baserad/agentisk visualisering

Det finns två alternativ för att distribuera beroende visualisering:

- **Agent-baserad**: agent-baserad beroende visualisering kräver att agenter installeras på varje lokal dator som du vill analysera.
- Utan **agent**: med det här alternativet behöver du inte installera agenter på datorer som du vill kryssa för. Det här alternativet är för närvarande en för hands version och är bara tillgängligt för virtuella VMware-datorer.


## <a name="agent-based-visualization"></a>Agent-baserad visualisering

**Krav** | **Detaljer** | **Läs mer**
--- | --- | ---
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer. | [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/><br/> [Lär dig hur](how-to-assess.md) du lägger till ett utvärderings verktyg i ett befintligt projekt.<br/><br/> Lär dig hur du konfigurerar Azure Migrate-enheten för utvärdering av [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Agenter som krävs** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till Internet måste du ladda ned och installera Log Analytics gateway på dem. | Läs mer om att installera [beroende agenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | Azure Migrate använder [tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) -lösningen i [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) för beroende visualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate-projekt. Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till. <br/><br/> Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.<br/><br/> Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.
**Täckt** | Tjänstkarta lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du associerar arbets ytan Log Analytics med Azure Migrate projektet)/<br/><br/> Efter 180 dagar kommer standard Log Analytics avgifter att gälla.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standardkostnader](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort, tas inte arbets ytan bort tillsammans med den. När du har tagit bort projektet är Tjänstkarta användning inte kostnads fritt, och varje nod debiteras enligt den betalda nivån för Log Analytics arbets yta/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA-28 februari 2018) kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa betalning efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor innan GA fortfarande kan debiteras.
**Hantering** | När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda Log Analytics arbets ytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbets ytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort Azure Migrate-projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.

## <a name="agentless-visualization"></a>Visualisering utan agent


**Krav** | **Detaljer** | **Läs mer**
--- | --- | ---
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala VMWare-datorer. | [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/><br/> [Lär dig hur](how-to-assess.md) du lägger till ett utvärderings verktyg i ett befintligt projekt.<br/><br/> Lär dig hur du konfigurerar Azure Migrate-installationen för utvärdering av virtuella [VMware](how-to-set-up-appliance-vmware.md) -datorer.
**Agenter som krävs** | Ingen agent krävs på de datorer som du vill analysera.
**Operativsystem som stöds** | Granska de [operativ system](migrate-support-matrix-vmware.md#agentless-dependency-visualization) som stöds för övervakning utan agent.
**Virtuella datorer** | **VMware-verktyg**: VMware-verktyg måste installeras och köras på de virtuella datorer som du vill analysera.<br/><br/> **Konto**: på Azure Migrate-enheten måste du lägga till ett användar konto som kan användas för att komma åt virtuella datorer för analys.<br/><br/> **Virtuella Windows-datorer**: användar kontot måste vara lokalt eller ha en domän administratör på datorn.<br/><br/> **Virtuella Linux-datorer**: rot privilegiet krävs för kontot. Alternativt måste användar kontot ha dessa två funktioner på/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE. | [Lär dig mer om](migrate-appliance.md) Azure Migrate-enheten.
**VMware** | **vCenter**: installationen behöver ett vCenter Server konto med skrivskyddad åtkomst och behörigheter som är aktiverade för Virtual Machines > gäst åtgärder.<br/><br/> **ESXi-värdar**: på ESXi-värdar som kör virtuella datorer som du vill analysera måste Azure Migrate-installationen kunna ansluta till TCP-port 443.
**Insamlade data** |  Analytiska beroende analyser fungerar genom att samla in TCP-anslutningsfel från datorer där den är aktive rad. När beroende identifiering har Aktiver ATS samlar enheten in TCP-anslutningsfel var femte minut från virtuella gäst datorer. Dessa data samlas in från virtuella gäst datorer via vCenter Server med vSphere-API: er. Insamlade data bearbetas på enheten för att härleda beroende information och skickas till Azure Migrate var 6: e timme. Följande data samlas in från varje dator: <br/> – Namn på processer som har aktiva anslutningar.<br/> – Namn på program som kör processen med aktiva anslutningar.<br/> -Mål port på de aktiva anslutningarna.


## <a name="next-steps"></a>Nästa steg
- [Konfigurera beroende visualisering](how-to-create-group-machine-dependencies.md)
- [Testa övervakning utan agent beroende](how-to-create-group-machine-dependencies-agentless.md) för virtuella VMware-datorer.
- Läs [vanliga frågor](common-questions-discovery-assessment.md#what-is-dependency-visualization) om beroende visualisering.


