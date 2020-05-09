---
title: Stöd för VMware-utvärdering i Azure Migrate
description: Läs mer om stöd för utvärdering av virtuella VMware-datorer med Azure Migrate Server-utvärdering.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a0d05c56670c54aca25232a86b5a0e89d2f0bcfd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983660"
---
# <a name="support-matrix-for-vmware-assessment"></a>Support mat ris för VMware-utvärdering 

Den här artikeln sammanfattar krav och support när du bedömer virtuella VMware-datorer för migrering till Azure med hjälp av verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Om du vill migrera virtuella VMware-datorer till Azure läser du [matrisen migration support](migrate-support-matrix-vmware-migration.md).

För att utvärdera virtuella VMware-datorer skapar du ett Azure Migrate-projekt och lägger sedan till verktyget Server bedömning i projektet. När du har lagt till verktyget distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala datorer och skickar metadata och prestanda data till Azure. När identifieringen är klar samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp.

## <a name="limitations"></a>Begränsningar

**Support** | **Information**
--- | ---
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration.<br/><br/> Du kan identifiera och utvärdera upp till 35 000 virtuella VMware-datorer i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects). Ett projekt kan även innehålla fysiska servrar och virtuella Hyper-V-datorer, upp till utvärderings gränserna för var och en.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.


## <a name="application-discovery"></a>Programidentifiering

Förutom att identifiera datorer kan Server utvärderingen identifiera appar, roller och funktioner som körs på datorer. Genom att identifiera din program inventering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar. 

**Support** | **Information**
--- | ---
**Datorer som stöds** | Identifiering av appar stöds för närvarande endast för virtuella VMware-datorer.
**Identifiering** | Identifiering av appar är agenten. Den använder autentiseringsuppgifter för maskin-gäst och fjärråtkomst till datorer via WMI och SSH-samtal.
**Stöd för virtuella datorer** | App-Discovery stöds för alla Windows-och Linux-versioner.
**autentiseringsuppgifter för vCenter** | För identifiering av appar krävs ett vCenter Server konto med skrivskyddad åtkomst, och behörigheter har Aktiver ATS för Virtual Machines > gäst åtgärder.
**Autentiseringsuppgifter för virtuell dator** | App Discovery stöder för närvarande att en autentiseringsuppgift används för alla Windows-servrar och en autentiseringsuppgift för alla Linux-servrar.<br/><br/> Du skapar ett gäst användar konto för virtuella Windows-datorer och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla virtuella Linux-datorer.
**VMware-verktyg** | VMware-verktyg måste installeras och köras på de virtuella datorer som du vill identifiera. <br/> VMware Tools-versionen måste vara senare än 10.2.0.
**PowerShell** | Virtuella datorer måste ha PowerShell version 2,0 eller senare installerat.
**Port åtkomst** | På ESXi-värdar som kör virtuella datorer som du vill identifiera måste Azure Migrate-installationen kunna ansluta till TCP-port 443.
**Begränsningar** | För app-Discovery kan du identifiera upp till 10000 virtuella datorer på varje Azure Migrate-apparat.



## <a name="vmware-requirements"></a>Krav för VMware

**VMware** | **Information**
--- | ---
**VMwares virtuella datorer** | Utvärderingen stöds för alla Windows-och Linux-operativsystem.
**vCenter Server** | Datorer som du vill identifiera och utvärdera måste hanteras av vCenter Server version 5,5, 6,0, 6,5 eller 6,7.
**Behörigheter (utvärdering)** | vCenter Server skrivskyddat konto.
**Behörigheter (app-Discovery)** | vCenter Server konto med skrivskyddad åtkomst och behörigheter som har Aktiver ATS för **virtuella datorer > gäst åtgärder**.
**Behörigheter (beroende visualisering)** | vCenter Server konto med skrivskyddad åtkomst och privilegier som är aktiverade för **virtuella datorer** > med**gäst verksamhet**.


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Du kan distribuera installationen som en virtuell VMWare-dator med hjälp av en ägg-mall, som importeras till vCenter Server eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- Lär dig mer om webb adresser som behövs för att få åtkomst till [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) moln.
- I Azure Government måste du distribuera enheten [med hjälp av skriptet](deploy-appliance-script-government.md).


## <a name="port-access"></a>Port åtkomst

**Enhet** | **Anslutning**
--- | ---
Enhet | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med hjälp av URL: en:```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443 (HTTPS) för att skicka identifierings-och prestanda-metadata till Azure Migrate.
vCenter Server | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.
ESXi-värdar (app Discovery/agent utan agent analys) | Om du vill göra en [app-identifiering](how-to-discover-applications.md) eller en [agent lös beroende analys](concepts-dependency-visualization.md#agentless-analysis)ansluter-enheten till ESXI-värdar på TCP-port 443, för att identifiera program, till och köra en agent lös beroende visualisering på virtuella datorer.

## <a name="application-discovery"></a>Programidentifiering

Förutom att identifiera datorer kan Server utvärderingen identifiera appar, roller och funktioner som körs på datorer. Genom att identifiera din program inventering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar. 

**Support** | **Information**
--- | ---
**Datorer som stöds** | Identifiering av appar stöds för närvarande endast för virtuella VMware-datorer.
**Identifiering** | Identifiering av appar är agenten. Den använder autentiseringsuppgifter för maskin-gäst och fjärråtkomst till datorer via WMI och SSH-samtal.
**Stöd för virtuella datorer** | App-Discovery stöds för alla Windows-och Linux-versioner.
**autentiseringsuppgifter för vCenter** | För identifiering av appar krävs ett vCenter Server konto med skrivskyddad åtkomst, och behörigheter har Aktiver ATS för Virtual Machines > gäst åtgärder.
**Autentiseringsuppgifter för virtuell dator** | App Discovery stöder för närvarande att en autentiseringsuppgift används för alla Windows-servrar och en autentiseringsuppgift för alla Linux-servrar.<br/><br/> Du skapar ett gäst användar konto för virtuella Windows-datorer och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla virtuella Linux-datorer.
**VMware-verktyg** | VMware-verktyg måste installeras och köras på de virtuella datorer som du vill identifiera. <br/> VMware Tools-versionen måste vara senare än 10.2.0.
**PowerShell** | Virtuella datorer måste ha PowerShell version 2,0 eller senare installerat.
**Port åtkomst** | På ESXi-värdar som kör virtuella datorer som du vill identifiera måste Azure Migrate-installationen kunna ansluta till TCP-port 443.
**Begränsningar** | För app-Discovery kan du identifiera upp till 10000 virtuella datorer på varje Azure Migrate-apparat.


## <a name="agentless-dependency-analysis-requirements"></a>Krav för beroende analys av agent

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. Tabellen sammanfattar kraven för att skapa en agent utan beroende analys. 

**Krav** | **Information**
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget för Server bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala VMWare-datorer.<br/><br/> [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/> [Lär dig hur](how-to-assess.md) du lägger till ett utvärderings verktyg i ett befintligt projekt.<br/> [Lär dig hur](how-to-set-up-appliance-vmware.md) du konfigurerar Azure Migrate-installationen för utvärdering av virtuella VMware-datorer.
**Stöd för virtuella datorer** | Stöds för närvarande endast för virtuella VMware-datorer.
**Virtuella Windows-datorer** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitars).
**Windows-konto** |  För beroende analys behöver Azure Migrate-enheten ett domän administratörs konto eller ett lokalt administratörs konto för att få åtkomst till virtuella Windows-datorer.
**Virtuella Linux-datorer** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-konto** | För beroende analys måste Azure Migrate-installationen ha ett användar konto med rot behörighet på Linux-datorer.<br/><br/> Alternativt behöver användar kontot dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.
**Agenter som krävs** | Ingen agent krävs på de datorer som du vill analysera.
**VMware-verktyg** | VMware-verktyg (senare än 10,2) måste installeras och köras på varje virtuell dator som du vill analysera.
**vCenter Server autentiseringsuppgifter** | Beroende visualisering behöver ett vCenter Server-konto med skrivskyddad åtkomst och behörigheter som är aktiverade för Virtual Machines > gäst åtgärder. 
**PowerShell** | Virtuella datorer måste ha PowerShell version 2,0 eller senare installerat.
**Port åtkomst** | På ESXi-värdar som kör virtuella datorer som du vill analysera måste Azure Migrate-installationen kunna ansluta till TCP-port 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Krav för agent-baserade beroende analyser

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. I tabellen sammanfattas kraven för att skapa en agent beroende analys. 

**Krav** | **Information** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer<br/><br/> [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/> [Lär dig hur](how-to-assess.md) du lägger till ett utvärderings verktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för utvärdering av [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Azure Government** | Beroende visualisering är inte tillgänglig i Azure Government.
**Log Analytics** | Azure Migrate använder [tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) -lösningen i [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) för beroende visualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate-projekt. Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till. <br/><br/> Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.<br/><br/> Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.
**Agenter som krävs** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till Internet måste du ladda ned och installera Log Analytics gateway på dem.<br/><br/> Läs mer om att installera [beroende agenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-arbetsyta** | Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
**Kostnader** | Tjänstkarta lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du associerar arbets ytan Log Analytics med Azure Migrate projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standardkostnader](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort, tas inte arbets ytan bort tillsammans med den. När du har tagit bort projektet är Tjänstkarta användning inte kostnads fritt, och varje nod debiteras enligt den betalda nivån för Log Analytics arbets yta/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA-28 februari 2018) kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa betalning efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor innan GA fortfarande kan debiteras.
**Hantering** | När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda Log Analytics arbets ytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbets ytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort Azure Migrate-projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.
**Internetanslutning** | Om datorerna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.
**Azure Government** | Agent-baserad beroende analys stöds inte.


## <a name="next-steps"></a>Nästa steg

- [Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar.
- [Förbered för VMware](tutorial-prepare-vmware.md) -utvärdering.
