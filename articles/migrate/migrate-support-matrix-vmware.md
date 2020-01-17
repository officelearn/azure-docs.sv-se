---
title: Stöd för VMware-utvärdering i Azure Migrate
description: Läs mer om stöd för VMware-utvärdering i Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 74dae71404fe827c9e19d5e3042afd2f98a7a5dd
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154694"
---
# <a name="support-matrix-for-vmware-assessment"></a>Support mat ris för VMware-utvärdering 

Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera virtuella VMware-datorer med [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-migration-tool). Om du vill ha information om hur du migrerar virtuella VMware-datorer till Azure läser du [matrisen migration support](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Översikt

Om du vill utvärdera lokala datorer för migrering till Azure med den här artikeln lägger du till verktyget Azure Migrate: Server utvärderings verktyg i ett Azure Migrate projekt. Du distribuerar [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala datorer och skickar konfigurations-och prestanda data till Azure. Efter dator identifiering samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp.


## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Utvärderings gränser**| Upptäck och utvärdera upp till 35 000 virtuella VMware-datorer i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration. Ett projekt kan omfatta virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar, upp till utvärderings gränserna.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.


## <a name="application-discovery"></a>Programidentifiering

Förutom att identifiera datorer kan Azure Migrate: Server utvärdering kan identifiera appar, roller och funktioner som körs på datorer. Genom att identifiera din program inventering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar. 

**Support** | **Detaljer**
--- | ---
**Identifiering** | Identifieringen är agent lösa, använder autentiseringsuppgifter för maskin-gäst och fjärråtkomst till datorer med WMI och SSH-samtal.
**Datorer som stöds** | Lokala virtuella VMware-datorer.
**Datorns operativ system** | Alla Windows-och Linux-versioner.
**autentiseringsuppgifter för vCenter** | Ett vCenter Server konto med skrivskyddad åtkomst och behörigheter som har Aktiver ATS för Virtual Machines > gäst åtgärder.
**Autentiseringsuppgifter för virtuell dator** | För närvarande har stöd för att använda en autentiseringsuppgift för alla Windows-servrar och en autentiseringsuppgift för alla Linux-servrar.<br/><br/> Du skapar ett gäst användar konto för virtuella Windows-datorer och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla virtuella Linux-datorer.
**VMware-verktyg** | VMware-verktyg måste installeras och köras på de virtuella datorer som du vill identifiera.
**Port åtkomst** | På ESXi-värdar som kör virtuella datorer som du vill identifiera måste Azure Migrate-installationen kunna ansluta till TCP-port 443.
**Begränsningar** | För app-Discovery kan du identifiera upp till 10000 per apparat. 

## <a name="vmware-requirements"></a>Krav för VMware

**VMware** | **Detaljer**
--- | ---
**vCenter Server** | Datorer som du vill identifiera och utvärdera måste hanteras av vCenter Server version 5,5, 6,0, 6,5 eller 6,7.
**Behörigheter (utvärdering)** | vCenter Server skrivskyddat konto.
**Behörigheter (app-Discovery)** | vCenter Server konto med skrivskyddad åtkomst och behörigheter som har Aktiver ATS för virtuella datorer > gäst åtgärder.
**Behörigheter (beroende visualisering)** | Center Server-konto med skrivskyddad åtkomst och privilegier som är aktiverade för **virtuella datorer** > **gäst åtgärder**.


## <a name="azure-migrate-appliance-requirements"></a>Krav för Azure Migrate utrustning

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Installationen av VMware distribueras med hjälp av en beredskaps mall som importeras till vCenter Server. 

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- Lär dig mer om [webb adresser](migrate-appliance.md#url-access) som behövs för att få åtkomst till enheten.

## <a name="port-access"></a>Port åtkomst

**Enhet** | **Anslutning**
--- | ---
Enhet | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
vCenter Server | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.

## <a name="agent-based-dependency-visualization"></a>Agent-baserad beroende visualisering

[Beroende visualisering](concepts-dependency-visualization.md) hjälper dig att visualisera beroenden mellan datorer som du vill utvärdera och migrera. För en agent-baserad visualisering sammanfattas krav och begränsningar i följande tabell


**Krav** | **Detaljer**
--- | ---
**Distribution** | Innan du distribuerar beroende visualisering bör du ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet. Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer.<br/><br/> Beroende visualisering är inte tillgänglig i Azure Government.
**Tjänstkarta** | Agent-baserad beroende visualisering använder [tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) lösning i [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Om du vill distribuera associerar du en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate projekt.
**Log Analytics-arbetsyta** | Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
**Avgifter** | Tjänstkarta-lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du kopplade arbets ytan Log Analytics med Azure Migrate-projektet).<br/><br/> Efter 180 dagar kommer standard Log Analytics avgifter att gälla.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras standard Log Analytics avgifter.<br/><br/> Om du tar bort Azure Migrate-projektet raderas inte arbets ytan med den. När du har tagit bort projektet är Tjänstkarta inte kostnads fritt, och varje nod debiteras enligt den betalda nivån i Log Analytics arbets ytan.
**Aktörer** | Agent-baserad beroende visualisering kräver att två agenter installeras på varje dator som du vill analysera.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [beroende agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Internetanslutning** | Om datorerna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.


## <a name="agentless-dependency-visualization"></a>Agentfri visualisering av beroenden

Detta alternativ förhandsvisas just nu. [Läs mer](how-to-create-group-machine-dependencies-agentless.md). Kraven sammanfattas i följande tabell.

**Krav** | **Detaljer**
--- | ---
**Distribution** | Innan du distribuerar beroende visualisering bör du ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet. Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer.
**Stöd för virtuella datorer** | Stöds för närvarande endast för virtuella VMware-datorer.
**Virtuella Windows-datorer** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitars)
**Virtuella Linux-datorer** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Windows-konto** |  Visualiseringen behöver ett användar konto med gäst åtkomst.
**Linux-konto** | Visualiseringen behöver ett användar konto med rot behörighet.<br/><br/> Alternativt behöver användar kontot dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.
**VM-agenter** | Ingen agent krävs på de virtuella datorerna.
**VMware-verktyg** | VMware-verktyg måste installeras och köras på de virtuella datorer som du vill analysera.
**autentiseringsuppgifter för vCenter** | Ett vCenter Server konto med skrivskyddad åtkomst och behörigheter som har Aktiver ATS för Virtual Machines > gäst åtgärder.
**Port åtkomst** | På ESXi-värdar som kör virtuella datorer som du vill analysera måste Azure Migrate-installationen kunna ansluta till TCP-port 443.



## <a name="next-steps"></a>Nästa steg

- [Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar.
- [Förbered för VMware](tutorial-prepare-vmware.md) -utvärdering.
