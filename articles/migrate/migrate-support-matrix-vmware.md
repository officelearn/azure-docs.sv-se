---
title: VMware-utvärderingsstöd i Azure Migrate
description: Lär dig mer om stöd för VMware VM-utvärdering med Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: e0172656d06075f89a7c3a06e8d4e9be94e6f5d0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389315"
---
# <a name="support-matrix-for-vmware-assessment"></a>Supportmatris för VMware-bedömning 

Den här artikeln sammanfattar krav på förutsättningar och support när du bedömer virtuella VMware-datorer för migrering till Azure med hjälp av verktyget Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Om du vill migrera virtuella virtuella datorer med VMware till Azure läser du [hjälpmatrisen för migreringsstöd](migrate-support-matrix-vmware-migration.md).

Om du vill utvärdera virtuella datorer med VMware skapar du ett Azure Migrate-projekt och lägger sedan till serverbedömningsverktyget i projektet. När verktyget har lagts till distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Installationen identifierar kontinuerligt lokala datorer och skickar datorns metadata och prestandadata till Azure. När identifieringen är klar samlar du identifierade datorer i grupper och kör en utvärdering för en grupp.

## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Projektbegränsningar** | Du kan skapa flera projekt i en Azure-prenumeration.<br/><br/> Du kan upptäcka och bedöma upp till 35 000 virtuella datorer i ett enda [projekt.](migrate-support-matrix.md#azure-migrate-projects) Ett projekt kan också innehålla fysiska servrar och virtuella hyper-virtuella datorer, upp till bedömningsgränserna för varje.
**Identifiering** | Azure Migrate-enheten kan identifiera upp till 10 000 virtuella virtuella datorer för VMware på en vCenter Server.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan bedöma upp till 35 000 virtuella datorer i en enda bedömning.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.


## <a name="application-discovery"></a>Programidentifiering

Förutom att upptäcka datorer kan Server Assessment identifiera appar, roller och funktioner som körs på datorer. Genom att identifiera ditt applager kan du identifiera och planera en migreringsväg som är skräddarsydd för lokala arbetsbelastningar. 

**Support** | **Detaljer**
--- | ---
**Maskiner som stöds** | Appidentifiering stöds för närvarande endast för virtuella datorer med VMware.
**Identifiering** | Appidentifiering är agentless. Den använder maskingästautentiseringsuppgifter och fjärråtkomsterar datorer med WMI- och SSH-anrop.
**Stöd för virtuell dator** | Appidentifiering stöds för alla Windows- och Linux-versioner.
**vCenter-autentiseringsuppgifter** | Appidentifiering behöver ett vCenter Server-konto med skrivskyddad åtkomst och privilegier aktiverade för virtuella datorer > gästoperationer.
**Autentiseringsuppgifter för virtuella datorer** | Appidentifiering stöder för närvarande användning av en autentiseringsuppgifter för alla Windows-servrar och en autentiseringsuppgifter för alla Linux-servrar.<br/><br/> Du skapar ett gästanvändarkonto för virtuella Windows-datorer och ett vanligt/normalt användarkonto (icke-sudo-åtkomst) för alla virtuella Linux-datorer.
**Verktyg för VMware** | VMware-verktyg måste installeras och köras på virtuella datorer som du vill upptäcka. <br/> VMware-verktygsversionen måste vara senare än 10.2.0.
**Powershell** | Virtuella datorer måste ha PowerShell version 2.0 eller senare installerat.
**Tillträde till port** | På ESXi-värdar som kör virtuella datorer som du vill identifiera måste Azure Migrate-enheten kunna ansluta till TCP-port 443.
**Gränser** | För appidentifiering kan du identifiera upp till 1 0000 virtuella datorer på varje Azure Migrate-installation.



## <a name="vmware-requirements"></a>Krav på VMware

**Vmware** | **Detaljer**
--- | ---
**VMwares virtuella datorer** | Utvärdering stöds för alla Windows- och Linux-operativsystem.
**vCenter Server** | Datorer som du vill identifiera och bedöma måste hanteras av vCenter Server version 5.5, 6.0, 6.5 eller 6.7.
**Behörigheter (bedömning)** | vCenter Server skrivskyddat konto.
**Behörigheter (appidentifiering)** | vCenter Server-konto med skrivskyddad åtkomst och privilegier aktiverade för **virtuella datorer > gästoperationer**.
**Behörigheter (beroendevisualisering)** | Center Server-konto med skrivskyddad åtkomst och privilegier aktiverade för **virtuella datorer** > **Gästoperationer**.


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrate-enheten](migrate-appliance.md) för identifiering och utvärdering. Du kan distribuera enheten som en VMWare-vm med en OVA-mall, importerad till vCenter Server eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Läs mer om [apparatkrav](migrate-appliance.md#appliance---vmware) för VMware.
- Läs mer om [webbadresser](migrate-appliance.md#url-access) som apparaten behöver komma åt.

## <a name="port-access"></a>Tillträde till port

**Enhet** | **Anslutning**
--- | ---
Apparaten | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärrskrivbordsanslutningar till apparaten.<br/><br/> Inkommande anslutningar på port 44368 för att fjärråtkomst till appen för hantering av apparater med hjälp av URL:en:```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443 (HTTPS), för att skicka identifierings- och prestandametadata till Azure Migrate.
vCenter Server | Inkommande anslutningar på TCP-port 443 så att installationen kan samla in konfigurations- och prestandametadata för utvärderingar. <br/><br/> Apparaten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.
ESXi-värdar (appidentifiering/agentlös beroendeanalys) | Om du vill göra [appidentifiering](how-to-discover-applications.md) eller [agentlös beroendeanalys](concepts-dependency-visualization.md#agentless-analysis)ansluter enheten till ESXi-värdar på TCP-port 443 för att identifiera program, till och köra agentlös beroendevisualisering på virtuella datorer.

## <a name="agentless-dependency-analysis-requirements"></a>Krav på analys av agentlösa beroenden

[Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill bedöma och migrera till Azure. Tabellen sammanfattar kraven för att ställa in agentlös beroendeanalys. 

**Krav** | **Detaljer**
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med serverbedömningsverktyget tillagt i projektet.<br/><br/>  Du distribuerar beroendevisualisering när du har konfigurerat en Azure Migrate-installation för att identifiera dina lokala VMWare-datorer.<br/><br/> [Läs om hur du](create-manage-projects.md) skapar ett projekt för första gången.<br/> [Läs om hur du](how-to-assess.md) lägger till ett bedömningsverktyg i ett befintligt projekt.<br/> [Lär dig hur du](how-to-set-up-appliance-vmware.md) konfigurerar Azure Migrate-enheten för bedömning av virtuella datorer med VMware.
**Stöd för virtuell dator** | Stöds endast för virtuella datorer med VMware.
**Virtuella Windows-datorer** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitars).
**Windows-konto** |  För beroendeanalys behöver Azure Migrate-enheten ett lokalt konto eller ett domänadministratörskonto för att komma åt virtuella windows-datorer.
**Virtuella Linux-datorer** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-konto** | För beroendeanalys behöver Azure Migrate-installationen på Linux-datorer ett användarkonto med root-behörighet.<br/><br/> Alternativt behöver användarkontot dessa behörigheter för /bin/netstat- och /bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.
**Nödvändiga agenter** | Ingen agent krävs på maskiner som du vill analysera.
**Verktyg för VMware** | VMware Tools (senare än 10,2) måste installeras och köras på varje virtuell dator som du vill analysera.
**autentiseringsuppgifter för vCenter Server** | Beroendevisualisering kräver ett vCenter Server-konto med skrivskyddad åtkomst och privilegier som är aktiverade för virtuella datorer > gästoperationer. 
**Powershell** | Virtuella datorer måste ha PowerShell version 2.0 eller högre installerat.
**Tillträde till port** | På ESXi-värdar som kör virtuella datorer som du vill analysera måste Azure Migrate-enheten kunna ansluta till TCP-port 443.

## <a name="agent-based-dependency-analysis-requirements"></a>Agentbaserade krav på beroendeanalys

[Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill bedöma och migrera till Azure. Tabellen sammanfattar kraven för att ställa in agentbaserad beroendeanalys. 

**Krav** | **Detaljer** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server Assessment som lagts till i projektet.<br/><br/>  Du distribuerar beroendevisualisering efter att ha konfigurerat en Azure Migrate-installation för att identifiera dina lokala datorer<br/><br/> [Läs om hur du](create-manage-projects.md) skapar ett projekt för första gången.<br/> [Läs om hur du](how-to-assess.md) lägger till ett bedömningsverktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för bedömning av [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Azure Government** | Beroendevisualisering är inte tillgängligt i Azure Government.
**Logga Analytics** | Azure Migrate [Service Map](../operations-management-suite/operations-management-suite-service-map.md) använder servicemappningslösningen i [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) för beroendevisualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics-arbetsyta med ett Azure Migrate-projekt. Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till. <br/><br/> Arbetsytan måste finnas i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbetsytan måste finnas i regionerna Östra USA, Sydostasien eller Västeuropa. Arbetsytor i andra regioner kan inte associeras med ett projekt.<br/><br/> Arbetsytan måste finnas i en region där [Service Map stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> I Logganalys taggas arbetsytan som är associerad med Azure Migrate med nyckeln Migreringsprojekt och projektnamnet.
**Nödvändiga agenter** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Beroendeagenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till internet måste du hämta och installera Log Analytics-gatewayen på dem.<br/><br/> Läs mer om hur du installerar [beroendeagenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-arbetsyta** | Arbetsytan måste finnas i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbetsytor som finns i regionerna Östra USA, Sydostasien och Västeuropa.<br/><br/>  Arbetsytan måste finnas i en region där [Service Map stöds](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till.
**Kostnader** | Service Map-lösningen medför inga avgifter för de första 180 dagarna (från den dag då du associerar Log Analytics-arbetsytan med Azure Migrate-projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Service Map på den associerade log analytics-arbetsytan medför [standardavgifter](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort tas arbetsytan inte bort tillsammans med den. När du har tagit bort projektet är användning av tjänstkartning inte gratis och varje nod debiteras enligt den betalda nivån på Log Analytics-arbetsytan/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA- 28 februari 2018), kan du ha ådragit dig ytterligare servicemappningsavgifter. För att säkerställa betalning efter endast 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbetsytor innan GA fortfarande är avgiftsbelagda.
**Hantering** | När du registrerar agenter på arbetsytan använder du ID och nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda log analytics-arbetsytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbetsytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbetsytan som skapats av Azure Migrate, såvida du inte tar bort Azure Migrate-projektet. Om du gör det kommer beroendevisualiseringsfunktionen inte att fungera som förväntat.
**Internetanslutning** | Om datorer inte är anslutna till internet måste du installera log analytics-gatewayen på dem.


## <a name="next-steps"></a>Nästa steg

- [Granska](best-practices-assessment.md) metodtips för att skapa utvärderingar.
- [Förbered dig för VMware-bedömning.](tutorial-prepare-vmware.md)
