---
title: Vanliga frågor och svar om Azure Migrate-apparat
description: Få svar på vanliga frågor om Azure Migrate-installationen.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 529ead1baa769ee3e71f6fcf77ef7e020ed196a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529696"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrera-apparat: Vanliga frågor

Den här artikeln besvarar vanliga frågor om Azure Migrate-installationen. Om du har andra frågor kontrollerar du dessa resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [identifiering, bedömning och beroendevisualisering](common-questions-discovery-assessment.md)
- Frågor om [servermigrering](common-questions-server-migration.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Vad är Azure Migrate-apparaten?

Azure Migrate-installationen är en lättinstallation som azure migrate: serverutvärderingsverktyget använder för att identifiera och bedöma lokala servrar. Azure Migrate: Server Migration verktyget använder också installationen för agentless migrering av lokala virtuella datorer med videoprogram.

Här är mer information om Azure Migrate-installationen:

- Apparaten distribueras lokalt som en virtuell dator eller en fysisk dator.
- Installationen identifierar lokala datorer och skickar kontinuerligt datormetadata och prestandadata till Azure Migrate.
- Identifiering av apparater är agentless. Ingenting är installerat på upptäckta maskiner.

[Läs mer](migrate-appliance.md) om apparaten.

## <a name="how-can-i-deploy-the-appliance"></a>Hur kan jag distribuera apparaten?

Apparaten kan användas på följande sätt:

- Använda en mall för virtuella datorer med VMware och virtuella hyper-V-datorer (OVA-mall för VMware eller VHD för Hyper-V).
- Om du inte vill använda en mall, eller om du är i Azure Government, kan du distribuera installationen för VMware eller Hyper-V med ett PowerShell-skript.
- För fysiska servrar distribuerar du alltid apparaten med ett skript.


## <a name="how-does-the-appliance-connect-to-azure"></a>Hur ansluter installationen till Azure?

Apparaten kan ansluta via internet eller med hjälp av Azure ExpressRoute med offentlig/Microsoft-peering.

## <a name="does-appliance-analysis-affect-performance"></a>Påverkar apparatanalysen prestanda?

Azure Migrerar installationsprofiler lokalt datorer kontinuerligt för att mäta prestandadata. Denna profilering har nästan ingen prestandapåverkan på profilerade maskiner.

## <a name="can-i-harden-the-appliance-vm"></a>Kan jag härda den virtuella datorn för apparaten?

När du använder den nedladdade mallen för att skapa den virtuella datorn för den virtuella enheten kan du lägga till komponenter (till exempel antivirus) i mallen om du lämnar de kommunikations- och brandväggsregler som krävs för Azure Migrate-enheten.

## <a name="what-network-connectivity-is-required"></a>Vilken nätverksanslutning krävs?


Installationen behöver åtkomst till Azure-url:er. [Granska](migrate-appliance.md#url-access) URL-listan.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlar apparaten in?

Mer information om data som Azure Migrate-installationen samlar in på virtuella datorer finns i följande artiklar:

- **VMware VM:** [Granska](migrate-appliance.md#collected-data---vmware) insamlade data. [
- **Hyper-V VM:** [Granska](migrate-appliance.md#collected-data---hyper-v) insamlade data.

## <a name="how-is-data-stored"></a>Hur lagras data?

Data som samlas in av Azure Migrate-enheten lagras på Azure-platsen där du skapade Azure Migrate-projektet.

Här är mer information om hur data lagras:

- De insamlade data lagras säkert i CosmosDB i en Microsoft-prenumeration. Data tas bort när du tar bort Azure Migrate-projektet. Lagring hanteras av Azure Migrate. Du kan inte specifikt välja ett lagringskonto för insamlade data.
- Om du använder [beroendevisualisering](concepts-dependency-visualization.md)lagras de data som samlas in i USA i en Azure Log Analytics-arbetsyta som skapats i din Azure-prenumeration. Data tas bort när du tar bort Log Analytics-arbetsytan i din prenumeration.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Hur mycket data laddas upp under kontinuerlig profilering?

Mängden data som skickas till Azure Migrate beror på flera parametrar. Ett Azure Migrate-projekt som har 10 datorer (var och en med en disk och ett nätverkskort) skickar till exempel cirka 50 MB data per dag. Det här värdet är ungefärlig. Det faktiska värdet varierar beroende på antalet datapunkter för diskarna och nätverkskorten. Om antalet datorer, diskar eller nätverkskort ökar är ökningen av data som skickas icke-linjär.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Är data krypterade i vila och under transport?

Ja, för båda:

- Metadata skickas säkert till Azure Migrate-tjänsten via Internet via HTTPS.
- Metadata lagras i en [Azure Cosmos-databas](../cosmos-db/database-encryption-at-rest.md) och i [Azure Blob-lagring](../storage/common/storage-service-encryption.md) i en Microsoft-prenumeration. Metadata krypteras i vila för lagring.
- Data för beroendeanalys krypteras också under överföring (av säker HTTPS). Den lagras på en Log Analytics-arbetsyta i din prenumeration. Data krypteras i vila för beroendeanalys.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hur ansluter apparaten till vCenter Server?

De här stegen beskriver hur apparaten ansluter till VMware vCenter Server:

1. Apparaten ansluter till vCenter Server (port 443) med hjälp av de autentiseringsuppgifter du angav när du konfigurerade apparaten.
2. Installationen använder VMware PowerCLI för att fråga vCenter Server för att samla in metadata om de virtuella datorer som hanteras av vCenter Server.
3. Installationen samlar in konfigurationsdata om virtuella datorer (kärnor, minne, diskar, nätverkskort) och prestandahistoriken för varje virtuell dator under den senaste månaden.
4. De insamlade metadata skickas till Azure Migrate: Server Assessment tool (över internet via HTTPS) för bedömning.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Kan Azure Migrate-enheten ansluta till flera vCenter-servrar?

Nej. Det finns en 1:1-mappning mellan en [Azure Migrate-installation](migrate-appliance.md) och vCenter Server. Om du vill identifiera virtuella datorer i flera vCenter Server-instanser måste du distribuera flera enheter. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Kan ett Azure Migrate-projekt ha flera enheter?
Ett projekt kan ha flera apparater anslutna till sig. En apparat kan dock bara associeras med ett projekt. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Kan Azure Migrate-enheten/replikeringsverktyget ansluta till samma vCenter?
Ja. Du kan lägga till både Azure Migrate-enheten (används för utvärdering och agentlös VMware-migrering) och replikeringsverktyget (som används för agentbaserad migrering av virtuella datorer med VMware) till samma vCenter-server.


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hur många virtuella datorer eller servrar kan jag upptäcka med en apparat?

Du kan upptäcka upp till 10 000 virtuella virtuella datorer, upp till 5 000 virtuella virtuella datorer och upp till 250 fysiska servrar med en enda apparat. Om du har fler datorer i din lokala miljö läser du om [skalning av en Hyper-V-bedömning,](scale-hyper-v-assessment.md) [skalning av en VMware-bedömning](scale-vmware-assessment.md)och [skalning av en fysisk serverbedömning](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Kan jag ta bort en apparat?

För närvarande stöds inte att ta bort en apparat från projektet.

Det enda sättet att ta bort installationen är att ta bort resursgruppen som innehåller Azure Migrate-projektet som är associerat med installationen.

Om du tar bort resursgruppen tas dock även andra registrerade enheter, det identifierade lagret, utvärderingarna och alla andra Azure-komponenter i resursgruppen som är associerade med projektet bort.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Kan jag använda apparaten med en annan prenumeration eller ett annat projekt?

När du har använt installationen för att initiera identifiering kan du inte konfigurera om den använda enheten med en annan Azure-prenumeration och du kan inte använda den i ett annat Azure Migrate-projekt. Du kan inte heller identifiera virtuella datorer på en annan instans av vCenter Server. Konfigurera en ny apparat för dessa uppgifter.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan jag konfigurera installationen av apparaten på en virtuell Azure-dator?

Nej. För närvarande stöds inte det här alternativet. 

## <a name="can-i-discover-on-an-esxi-host"></a>Kan jag upptäcka på en ESXi-värd?

Nej. Om du vill identifiera virtuella datorer med VMware måste du ha vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Hur uppdaterar jag apparaten?

Som standard uppdateras apparaten och dess installerade agenter automatiskt. Apparaten söker efter uppdateringar var 24:e timme. Uppdateringar som inte kan göras igen. 

Endast apparaten och apparatens agenter uppdateras av dessa automatiska uppdateringar. Operativsystemet uppdateras inte av automatiska uppdateringar för Azure Migrate. Använd Windows-uppdateringar för att hålla operativsystemet uppdaterat.

## <a name="can-i-check-agent-health"></a>Kan jag kontrollera agent hälsa?

Ja. Gå till **hälsosidan** för Agent för azure migrate: Server assessment eller Azure Migrate: Server Migration tool i portalen. Där kan du kontrollera anslutningsstatusen mellan Azure och identifierings- och bedömningsagenter på installationen.

## <a name="next-steps"></a>Nästa steg

Läs [översikten över Azure Migrate](migrate-services-overview.md).
