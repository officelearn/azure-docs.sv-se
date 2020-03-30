---
title: Stöd för fysisk serverutvärdering i Azure Migrate
description: Lär dig mer om stöd för fysisk serverutvärdering med Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: f6c70ac2517a29497f4f11073e4b16067bab9576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336884"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Stödmatris för fysisk serverbedömning 

Den här artikeln sammanfattar förutsättningar och supportkrav för att bedöma fysiska servrar som förberedelse för migrering till Azure. Om du vill migrera fysiska servrar till Azure läser du [distributionsstödmatrisen](migrate-support-matrix-physical-migration.md).


Du bedömer fysiska servrar med verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Du skapar ett Azure Migrate-projekt och lägger sedan till verktyget i projektet. När verktyget har lagts till distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Installationen identifierar kontinuerligt lokala datorer och skickar datorns metadata och prestandadata till Azure. Efter datoridentifiering samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp.


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
| **Operativsystem** | Alla [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux-operativsystem](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) stöds förutom Windows Server 2003 och SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrate-enheten](migrate-appliance.md) för identifiering och utvärdering. Apparaten för fysiska servrar kan köras på en virtuell dator eller en fysisk dator. Du konfigurerar det med ett PowerShell-skript som du hämtar från Azure-portalen.

- Läs mer om [installationskrav](migrate-appliance.md#appliance---physical) för fysiska servrar.
- Läs mer om [webbadresser](migrate-appliance.md#url-access) som apparaten behöver komma åt.

## <a name="port-access"></a>Tillträde till port

I följande tabell sammanfattas portkrav för bedömning.

**Enhet** | **Anslutning**
--- | ---
**Apparaten** | Inkommande anslutningar på TCP-port 3389, för att tillåta fjärrskrivbordsanslutningar till apparaten.<br/><br/> Inkommande anslutningar på port 44368 för att fjärransluta till appen för hantering av apparater med hjälp av URL:en:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Utgående anslutningar på portar 443 (HTTPS), för att skicka identifierings- och prestandametadata till Azure Migrate.
**Fysiska servrar** | **Windows:** Inkommande anslutningar på WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations- och prestandametadata från Windows-servrar. <br/><br/> **Linux:**  Inkommande anslutningar på port 22 (UDP), för att hämta konfigurations- och prestandametadata från Linux-servrar. |

## <a name="agentless-dependency-analysis-requirements"></a>Krav på analys av agentlösa beroenden

[Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill bedöma och migrera till Azure. Tabellen sammanfattar kraven för att ställa in agentlös beroendeanalys. 


**Krav** | **Detaljer** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server Assessment som lagts till i projektet.<br/><br/>  Du distribuerar beroendevisualisering när du har konfigurerat en Azure Migrate-installation för att identifiera dina lokala VMWare-datorer.<br/><br/> [Läs om hur du](create-manage-projects.md) skapar ett projekt för första gången.<br/> [Läs om hur du](how-to-assess.md) lägger till ett bedömningsverktyg i ett befintligt projekt.<br/> [Lär dig hur du](how-to-set-up-appliance-vmware.md) konfigurerar Azure Migrate-enheten för bedömning av virtuella datorer med VMware.
**Stöd för virtuell dator** | Stöds endast för virtuella datorer med VMware.
**Virtuella Windows-datorer** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitars).
**Windows-konto** |  För beroendeanalys behöver Azure Migrate-enheten ett lokalt konto eller ett domänadministratörskonto för att komma åt virtuella windows-datorer.
**Virtuella Linux-datorer** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-konto** | För beroendeanalys behöver Azure Migrate-installationen på Linux-datorer ett användarkonto med root-behörighet.<br/><br/> Alternativt behöver användarkontot dessa behörigheter för /bin/netstat- och /bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.
**Nödvändiga agenter** | Ingen agent krävs på maskiner som du vill analysera.
**Verktyg för VMware** |  VMware Tools (senare än 10,2) måste installeras och köras på varje virtuell dator som du vill analysera.
**vCenter Server** |  Beroendevisualisering kräver ett vCenter Server-konto med skrivskyddad åtkomst och privilegier som är aktiverade för virtuella datorer > gästoperationer. **ESXi-värdar:** På ESXi-värdar som kör virtuella datorer som du vill analysera måste Azure Migrate-enheten kunna ansluta till TCP-port 443.

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

[Förbered dig för fysisk serverbedömning](tutorial-prepare-physical.md).
