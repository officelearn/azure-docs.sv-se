---
title: Vanliga frågor om Azure Migrate-enheten
description: Få svar på vanliga frågor om Azure Migrate-enheten
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c9727287160f996059e9c1fb2d1fb5aec5900ab5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292340"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate utrustning: vanliga frågor

I den här artikeln besvaras vanliga frågor om Azure Migrate-enheten. Om du har fler frågor efter att ha läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum). Om du har andra frågor kan du läsa följande artiklar:

- [Allmänna frågor](resources-faq.md) om Azure Migrate.
- [Frågor](common-questions-discovery-assessment.md) om identifiering, utvärdering och beroende visualisering.


## <a name="what-is-the-azure-migrate-appliance"></a>Vad är Azure Migrates apparaten?

Azure Migrate-installationen är en förenklad utrustning som används av Azure Migrate: Server utvärderings verktyg för att identifiera och utvärdera lokala servrar, och som används av Azure Migrate: Migreringsverktyg för Server för migrering utan agent för lokala virtuella VMware-datorer. 

Enheten distribueras lokalt som en virtuell dator eller fysisk dator. Enheten identifierar lokala datorer och skickar kontinuerligt metadata och prestanda data till Azure Migrate. Identifiering av enheter är agenten. Inget har installerats på identifierade datorer. [Läs mer](migrate-appliance.md) om enheten.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hur ansluter-enheten till Azure?

Anslutningen kan vara över Internet eller använda Azure-ExpressRoute med offentlig/Microsoft-peering.

## <a name="does-appliance-analysis-impact-performance"></a>Påverkar enhets analysen prestanda?

Azure Migrate utrustnings profiler lokalt för att mäta prestanda data för virtuella datorer kontinuerligt. Den här profileringen har nästan ingen prestanda påverkan på Hyper-V/ESXi-värdar eller på VMware vCenter Server.

### <a name="can-i-harden-the-appliance-vm"></a>Kan jag härdning av den virtuella datorn?

När du skapar en virtuell dator med den nedladdade mallen kan du lägga till komponenter (till exempel ett antivirus program) i mallen, förutsatt att du lämnar de kommunikations-och brand Väggs regler som krävs för Azure Migrate-installationen.


## <a name="what-network-connectivity-is-needed"></a>Vilken nätverks anslutning krävs?

Granska följande:
- VMware-utvärdering med Azure Migrate-utrustning: [URL](migrate-appliance.md#url-access) -och [port](migrate-support-matrix-vmware.md#port-access) åtkomst krav.
- VMware-agent utan migrering med hjälp av Azure Migrate-installationen: [URL](migrate-appliance.md#url-access) -och [port](migrate-support-matrix-vmware-migration.md#agentless-ports) åtkomst krav.
- Hyper-V-utvärdering med Azure Migrate-utrustning: [URL](migrate-appliance.md#url-access) -och [port](migrate-support-matrix-hyper-v.md#port-access) åtkomst krav.


## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlas in av enheten?

Granska insamlade data:

- [Prestanda data](migrate-appliance.md#collected-performance-data-vmware) och [metadata](migrate-appliance.md#collected-metadata-vmware)för VMware VM.
- [Prestanda data](migrate-appliance.md#collected-performance-data-hyper-v) och [metadata](migrate-appliance.md#collected-metadata-hyper-v)för Hyper-V VM.


## <a name="how-is-data-stored"></a>Hur lagras data?

Data som samlas in av Azure Migrates enheten lagras på den Azure-plats som du väljer när du skapade migreringsjobbet. 

- Data lagras säkert i en Microsoft-prenumeration och tas bort när du tar bort Azure Migrate-projektet.
- Om du använder [beroende visualisering](concepts-dependency-visualization.md)lagras data som samlas in i USA i en Log Analytics arbets yta som skapats i Azure-prenumerationen. Dessa data tas bort när du tar bort Log Analytics arbets ytan i din prenumeration.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Hur mycket data överförs i kontinuerlig profilering?

Mängden data som skickas till Azure Migrate beror på ett antal parametrar. För att ge dig en uppfattning om volymen skickar ett Azure Migrate-projekt med 10 datorer (var och en med en disk och ett nätverkskort) cirka 50 MB per dag. Värdet är ungefärligt och ändras baserat på antalet data punkter för nätverkskorten och diskarna. Ökningen av data som skickas är icke-linjär om antalet datorer, nätverkskort eller diskar ökar.

## <a name="is-data-encrypted-at-restin-transit"></a>Är data krypterade vid rest/under överföring?

Ja, för båda.

- Metadata skickas säkert till Azure Migrate tjänsten via Internet via HTTPS.
- Metadata lagras i en [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -databas och i [Azure Blob Storage](../storage/common/storage-service-encryption.md)i en Microsoft-prenumeration. Metadata är krypterade i vila.
- Data för beroende analys krypteras också i överföring (säker HTTPS). Den lagras i en Log Analytics arbets yta i din prenumeration. Det är också krypterat i vila.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hur ansluter-enheten till vCenter Server?

1. Enheten ansluter till vCenter Server (port 443) med de autentiseringsuppgifter du angav när du konfigurerade installationen.
2. Enheten använder VMware PowerCLI för att fråga vCenter Server, för att samla in metadata om de virtuella datorer som hanteras av vCenter Server.
3. Installations programmet samlar in konfigurations data om virtuella datorer (kärnor, minne, diskar, nätverkskort) och prestanda historiken för varje virtuell dator under den senaste månaden.
4. De insamlade metadata visas i Azure Migrate: Server utvärdering (via Internet via HTTPS) för utvärdering.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Kan jag ansluta enheten till flera vCenter-servrar?

Nej. Det finns en en-till-en-mappning mellan en installation och vCenter Server. Du måste distribuera flera enheter för att kunna identifiera virtuella datorer på flera vCenter Server-instanser.

### <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hur många virtuella datorer eller servrar kan jag identifiera med en apparat?

Du kan identifiera upp till 10 000 virtuella VMware-datorer, upp till 5 000 virtuella Hyper-V-datorer och upp till 250-servrar med en enda apparat. Om du har fler datorer i din lokala miljö kan du läsa om skalning av [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) och [fysisk](scale-physical-assessment.md) bedömning.

## <a name="can-i-delete-an-appliance"></a>Kan jag ta bort en installation?

Det finns inte stöd för att ta bort program från projektet.

- Det enda sättet att ta bort installationen är att ta bort resurs gruppen som innehåller det Azure Migrate projekt som är associerat med enheten.
- Men om du tar bort resurs gruppen raderas även andra registrerade enheter, den identifierade inventeringen, utvärderingen och alla andra Azure-komponenter som är kopplade till projektet i resurs gruppen.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Kan jag använda enheten med en annan prenumeration/ett annat projekt?

När du har använt enheten för att initiera identifieringen kan du inte konfigurera om den med en annan Azure-prenumeration eller i ett annat Azure Migrate projekt. Du kan inte heller identifiera virtuella datorer på en annan vCenter Server. Konfigurera en ny installation för dessa uppgifter.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan jag konfigurera enheten på en virtuell Azure-dator?
Stöds inte för närvarande. 

## <a name="can-i-discover-on-an-esxi-host"></a>Kan jag identifiera på en ESXi-värd?
Du behöver inte ett vCenter Server för att identifiera virtuella VMware-datorer.

## <a name="how-do-i-update-the-appliance"></a>Hur gör jag för att du uppdatera installationen?

Som standard uppdateras enheten och dess installerade agenter automatiskt. Enheten söker efter uppdateringar var 24: e timme. Om det uppstår några fel under uppdaterings processen finns det en process för att försöka igen. Automatiska uppdateringar uppdaterar endast utrustnings-och apparat agenter. Operativ systemet har inte uppdaterats. Använd Microsoft Updates för att hålla operativ systemet uppdaterat.

## <a name="can-i-check-agent-health"></a>Kan jag kontrol lera agent hälsan?

På portalen går du till sidan **agent hälsa** i Server utvärderingen eller Migreringsverktyg för Server. Där kan du kontrol lera anslutnings status mellan identifierings-och utvärderings agenter på enheten och Azure.

## <a name="next-steps"></a>Nästa steg
Läs [Azure Migrate översikt](migrate-services-overview.md).
