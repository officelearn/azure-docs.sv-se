---
title: Stöd för Hyper-V-utvärdering i Azure Migrate
description: Lär dig mer om stöd för Hyper-V-utvärdering med Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: e8a698b110f19dff593a93a41e9d6f20eb80cdb0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389009"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Stödmatris för Hyper-V-bedömning

Den här artikeln sammanfattar krav på förutsättningar och support när du bedömer virtuella hyper-virtuella datorer för migrering till Azure med verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Om du vill migrera virtuella virtuella hyper-v-datorer till Azure läser du [i migreringsstödmatrisen](migrate-support-matrix-hyper-v-migration.md).

Om du vill konfigurera Hyper-V VM-utvärdering skapar du ett Azure Migrate-projekt och lägger till serverbedömningsverktyget i projektet. När verktyget har lagts till distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Installationen identifierar kontinuerligt lokala datorer och skickar datorns metadata och prestandadata till Azure. När identifieringen är klar samlar du identifierade datorer i grupper och kör en utvärdering för en grupp.


## <a name="limitations"></a>Begränsningar

**Support** | **Detaljer**
--- | ---
**Bedömningsgränser** | Du kan identifiera och bedöma upp till 35 000 virtuella hyper-virtuella datorer i ett enda [Azure Migrate-projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projektbegränsningar** | Du kan skapa flera projekt i en Azure-prenumeration. Förutom virtuella hyper-virtuella datorer kan ett projekt innehålla virtuella datorer med VMware och fysiska servrar, upp till bedömningsgränserna för varje.
**Identifiering** | Azure Migrate-enheten kan identifiera upp till 5 000 virtuella virtuella hyper-v-datorer.<br/><br/> Apparaten kan ansluta till upp till 300 Hyper-V-värdar.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan bedöma upp till 35 000 virtuella datorer i en enda bedömning för en grupp.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.



## <a name="hyper-v-host-requirements"></a>Hyper-V-värdkrav

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Hyper-V-värd**       | Hyper-V-värden kan vara fristående eller distribueras i ett kluster.<br/><br/> Hyper-V-värden kan köra Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2.<br/> Du kan inte utvärdera virtuella datorer som finns på Hyper-V-värdar som kör Windows Server 2012.
| **Behörigheter**           | Du behöver administratörsbehörighet för Hyper-V-värden. <br/> Om du inte vill tilldela administratörsbehörighet skapar du ett lokalt användarkonto eller ett domänanvändarkonto och lägger till användarkontot i dessa grupper– Fjärrhanteringsanvändare, Hyper-V-administratörer och Prestandaövervakare. |
| **PowerShell-fjärranvändning**   | [PowerShell-ommotning](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) måste vara aktiverat på varje Hyper-V-värd. |
| **Hyper-V-replikering**       | Om du använder Hyper-V Replica (eller om du har flera virtuella datorer med samma VM-identifierare) och upptäcker både de ursprungliga och replikerade virtuella datorerna med Azure Migrate, kanske den bedömning som genereras av Azure Migrate inte är korrekt. |


## <a name="hyper-v-vm-requirements"></a>Hyper-V VM-krav

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux-operativsystem.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) |
| **Integration Services**       | [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på virtuella datorer som du bedömer för att kunna samla in operativsystemsinformation. |


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrate-enheten](migrate-appliance.md) för identifiering och utvärdering. Du kan distribuera enheten med en komprimerad Hyper-V VHD som du hämtar från portalen eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Läs mer om [apparatkrav](migrate-appliance.md#appliance---hyper-v) för Hyper-V.
- Läs mer om [webbadresser](migrate-appliance.md#url-access) som apparaten behöver komma åt.

## <a name="port-access"></a>Tillträde till port

I följande tabell sammanfattas portkrav för bedömning.

**Enhet** | **Anslutning**
--- | ---
**Apparaten** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärrskrivbordsanslutningar till apparaten.<br/><br/> Inkommande anslutningar på port 44368 för att fjärråtkomst till appen för hantering av apparater med hjälp av URL:en:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Utgående anslutningar på portar 443 (HTTPS), för att skicka identifierings- och prestandametadata till Azure Migrate.
**Hyper-V-värd/kluster** | Inkommande anslutningar på WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta metadata och prestandadata för virtuella hyper-virtuella datorer med hjälp av en CIM-session (Common Information Model).

## <a name="agent-based-dependency-analysis-requirements"></a>Agentbaserade krav på beroendeanalys

[Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill bedöma och migrera till Azure. Tabellen sammanfattar kraven för att ställa in agentbaserad beroendeanalys. Hyper-V stöder för närvarande endast agentbaserad beroendevisualisering. 

**Krav** | **Detaljer** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med serverbedömningsverktyget tillagt i projektet.<br/><br/>  Du distribuerar beroendevisualisering efter att ha konfigurerat en Azure Migrate-installation för att identifiera dina lokala datorer<br/><br/> [Läs om hur du](create-manage-projects.md) skapar ett projekt för första gången.<br/> [Läs om hur du](how-to-assess.md) lägger till ett bedömningsverktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för bedömning av [virtuella hyper-virtuella datorer](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Beroendevisualisering är inte tillgängligt i Azure Government.
**Logga Analytics** | Azure Migrate [Service Map](../operations-management-suite/operations-management-suite-service-map.md) använder servicemappningslösningen i [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) för beroendevisualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics-arbetsyta med ett Azure Migrate-projekt. Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till. <br/><br/> Arbetsytan måste finnas i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbetsytan måste finnas i regionerna Östra USA, Sydostasien eller Västeuropa. Arbetsytor i andra regioner kan inte associeras med ett projekt.<br/><br/> Arbetsytan måste finnas i en region där [Service Map stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> I Logganalys taggas arbetsytan som är associerad med Azure Migrate med nyckeln Migreringsprojekt och projektnamnet.
**Nödvändiga agenter** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Beroendeagenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till internet måste du hämta och installera Log Analytics-gatewayen på dem.<br/><br/> Läs mer om hur du installerar [beroendeagenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-arbetsyta** | Arbetsytan måste finnas i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbetsytor som finns i regionerna Östra USA, Sydostasien och Västeuropa.<br/><br/>  Arbetsytan måste finnas i en region där [Service Map stöds](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till.
**Kostnader** | Service Map-lösningen medför inga avgifter för de första 180 dagarna (från den dag då du associerar Log Analytics-arbetsytan med Azure Migrate-projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Service Map på den associerade log analytics-arbetsytan medför [standardavgifter](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort tas arbetsytan inte bort tillsammans med den. När du har tagit bort projektet är användning av tjänstkartning inte gratis och varje nod debiteras enligt den betalda nivån på Log Analytics-arbetsytan/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA- 28 februari 2018), kan du ha ådragit dig ytterligare servicemappningsavgifter. För att säkerställa betalning efter endast 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbetsytor innan GA fortfarande är avgiftsbelagda.
**Hantering** | När du registrerar agenter på arbetsytan använder du ID och nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda log analytics-arbetsytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbetsytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbetsytan som skapats av Azure Migrate, såvida du inte tar bort Azure Migrate-projektet. Om du gör det kommer beroendevisualiseringsfunktionen inte att fungera som förväntat.
**Internetanslutning** | Om datorer inte är anslutna till internet måste du installera log analytics-gatewayen på dem.

## <a name="next-steps"></a>Nästa steg

[Förbered för utvärdering av virtuella datorer med hyper V](tutorial-prepare-hyper-v.md)
