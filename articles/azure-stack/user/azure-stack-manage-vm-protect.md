---
title: Skydda virtuella datorer som distribueras på Azure Stack | Microsoft Docs
description: Riktlinjer för hur du skyddar virtuella datorer som distribueras på Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: ab55ed73c7364b48f3159672ebee5d934365c92c
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191537"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Skydda virtuella datorer som distribueras på Azure Stack

Använd den här artikeln som vägledning för att utveckla en plan för att skydda virtuella datorer (VM) som dina användare distribuerar på Azure Stack.

Du behöver implementera en plan för säkerhetskopiering, återställning eller katastrofåterställning för sina data och program för att skydda dig mot dataförlust och oplanerade avbrott. Den här planen kan vara unikt för varje program men följer ett ramverk som upprättats av din organisations omfattande affärskontinuitet och haveriberedskap (BC/DR). En bra utgångspunkt är [designa elastiska program för Azure](https://docs.microsoft.com/azure/architecture/resiliency), som tillhandahåller allmänna mönster och metoder för programmets tillgänglighet och återhämtning.

>[!IMPORTANT]
> Testa din backup- och haveriberedskap planer med jämna mellanrum. Nu måste du så här kontrollerar du att:
> * Planer arbetet
> * Prenumerationerna fortfarande uppfyller kraven som de utformades för.

## <a name="azure-stack-infrastructure-recovery"></a>Återställning av Azure Stack-infrastruktur

Användare som är ansvariga för att skydda sina virtuella datorer separat från Azure Stack-infrastrukturtjänster.

Återställningsplan för Azure Stack infrastrukturtjänster **inte** omfattar återställning av användarnas VM: ar, lagringskonton och databaser. Du är ansvarig för att implementera en återställningsplan för dina program och data som ägare till programmet.

Om Azure Stack-molnet är offline en längre tid eller permanent oåterkalleligt, måste du ha en återställningsplan placera som:

* Säkerställer minimal avbrottstid
* Ser till att kritiska virtuella datorer, till exempel database-servrar som kör
* Gör att betjäna begäranden mellan användare och-program

Operatorn i Azure Stack-molnet är ansvarig för att skapa en återställningsplan för de underliggande Azure Stack-infrastruktur och tjänster. Mer information finns i artikeln [fort oåterkallelig dataförlust](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Källa/mål kombinationer

Varje Azure Stack-molnet har distribuerats till ett datacenter. Det krävs en separat miljö så att du kan återställa dina program. Återställningsmiljön kan vara en annan Azure Stack-moln i ett annat datacenter eller i Azures offentliga moln. Din datasuveränitet och datasekretesskrav avgör Återställningsmiljö för ditt program. När du aktiverar skydd för varje program har du flexibiliteten att välja en specifik återställningsalternativet för var och en. Du kan ha program i en prenumeration som säkerhetskopierar data till ett annat datacenter. Du kan replikera data till Azure-offentliga molnet i en annan prenumeration.

Planera din strategi för säkerhetskopiering, återställning och katastrofåterställning för varje program att fastställa mål för varje program. En återställningsplan hjälper din organisation korrekt storlek storage kapacitet som krävs för lokala platser och projekt förbrukning i det offentliga molnet.

|  | Global Azure | Azure Stack som distribueras till CSP-datacentret och drivs av CSP: N | Azure Stack som distribueras i kundernas datacenter och drivs av kunden |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack som distribueras till CSP-datacentret och drivs av CSP: N** | Användare VMs distribueras till CSP drivs Azure Stack.<br><br>Användare VMs återställts från en säkerhetskopia eller växlas över direkt till Azure. | CSP: N fungerar de primära och sekundära instanserna av Azure Stack i sina egna datacenter.<br><br>Användare VMs återställas eller redundansväxlats mellan två Azure Stack-instanser. | CSP: N fungerar Azure Stack på den primära platsen.<br><br>Kundens datacenter är målet för återställning eller redundans. |
| **Azure Stack som distribueras i kundernas datacenter och drivs av kunden** | Användare VMs distribueras till kunden drivs av Azure Stack.<br><br>Användare VMs återställts från en säkerhetskopia eller växlas över direkt till Azure. | Kunden fungerar Azure Stack på den primära platsen.<br><br>CSP-datacenter är målet för återställning eller redundans. | Kunden fungerar de primära och sekundära instanserna av Azure Stack i sina egna datacenter.<br><br>Användare VMs återställas eller redundansväxlats mellan två Azure Stack-instanser. |

![Källa / mål-kombinationer](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Återställningsmål för program

Du måste fastställa mängden stopptid och dataförlust som din organisation kan tolerera för varje program. Du kan kvantifiera stopptid och dataförlust för att skapa en återställningsplan som minimerar påverkan av ett haveri på din organisation. Överväg att för varje program:

 - **Återställningstid (RTO)**  
RTO är den tid som ett program kan vara otillgängligt efter en incident. Till exempel innebär en RTO 90 minuter att du måste kunna återställa programmet till ett fungerande tillstånd inom 90 minuter från början av ett haveri. Om du har ett lågt RTO kan du behålla en andra distribution som körs kontinuerligt i vänteläge för att skydda mot ett regionalt strömavbrott.
 - **Mål för återställningspunkt (RPO)**  
Återställningspunktmålet är den maximala dataförlusten som är acceptabel vid ett haveri. Om du till exempel lagrar data i en enda databas, utan replikering till andra databaser, och säkerhetskopierar varje timme kan du förlora upp till en timme med data.

RTO och RPO handlar om affärsbehov. Utför en riskbedömning för att definiera programmets RTO och RPO.

Ett annat mått är **tiden för Återställ** (MTTR), vilket är den genomsnittliga tiden det tar för att återställa programmet efter ett fel. MTTR är ett empiriska värde för ett system. Om MTTR är längre än RTO så skapar fel i systemet oacceptabla affärsstörningar, eftersom det inte går att återställa systemet inom definierad RTO.

### <a name="backup-restore"></a>Backup-återställning

Det vanligaste protection schemat för VM-baserade program är att använda programvara för säkerhetskopiering. Säkerhetskopiera en virtuell dator vanligtvis innehåller operativsystemet, konfiguration av operativsystem, programmets binärfiler och programdata. Säkerhetskopiorna som skapas genom att ta en ögonblicksbild av volymerna eller diskarna hel virtuell dator. Med Azure Stack, har du flexibiliteten att säkerhetskopiera från inom ramen för gästoperativsystemet eller från Azure Stack-lagring och beräkning API: er. Azure Stack har inte stöd för att möjliggöra säkerhetskopieringar på hypervisornivå.
 
![Backup-restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Återställa programmet kräver att återställa en eller flera virtuella datorer i samma moln eller på ett nytt moln. Du kan använda ett moln i ditt datacenter eller det offentliga molnet. Molnet som du väljer är helt inom din kontroll och baseras på dina krav på sekretess och datasuveränitet.
 
 - RTO: Nedtid som mäts i timmar
 - RPO: Variabeln dataförlust (beroende på säkerhetskopieringsfrekvensen)
 - Topologi för distribution: Aktiv/passiv

#### <a name="planning-your-backup-strategy"></a>Planera din strategi för säkerhetskopiering

Planera din strategi för säkerhetskopiering och definiera skalningskrav börjar du med är lika med antalet instanser av virtuella datorer som behöver skyddas. Säkerhetskopiera alla virtuella datorer för alla servrar i en miljö är en gemensam strategi. Med Azure Stack finns det dock vissa virtuella datorer som behöver säkerhetskopieras. Till exempel betraktas virtuella datorer i en skalningsuppsättning som tillfälliga resurser som kan komma och ibland gå utan föregående meddelande. Beständiga data som måste skyddas lagras i en separat lagringsplats, till exempel en databas eller ett objekt butik.

Viktigt att tänka på för att säkerhetskopiera virtuella datorer på Azure Stack:

 - **Kategorisering**
    - Överväg en modell där användarna välja att säkerhetskopiering av virtuella datorer.
    - Definiera ett recovery servicenivåavtal (SLA) baserat på prioritet av program eller påverkan på verksamheten.
 - **Skalning**
    - Överväg att successiva säkerhetskopior vid registreringen av ett stort antal nya virtuella datorer (om det krävs säkerhetskopia).
    - Utvärdera säkerhetskopiering produkter som effektivt kan samla in och överföra säkerhetskopierade data för att minimera Resursinnehåll på lösningen.
    - Utvärdera säkerhetskopiering produkter som effektivt lagrar säkerhetskopierade data med inkrementella eller differentiella säkerhetskopieringar för att minimera behovet av fullständiga säkerhetskopieringar för samtliga virtuella datorer i miljön.
 - **Återställ**
    - Säkerhetskopiering produkter kan återställa virtuella diskar, programdata i en befintlig virtuell dator eller hela VM-resurs och associerade virtuella diskar. Restore-schema som du behöver beror på hur du planerar att återställa programmet och den påverkar programmet tiden till återställning. Exempelvis kan vara det lättare att distribuera om SQLServer från en mall och sedan återställa databaser i stället för att återställa en hel virtuell dator eller uppsättning virtuella datorer.

### <a name="replicationmanual-failover"></a>Replikering/manuell redundans

En alternativ metod för att stöd för hög tillgänglighet är att replikera dina program på virtuella datorer till ett annat moln och förlitar sig på en manuell redundansväxling. Replikering av operativsystemet, programmets binärfiler och programdata kan utföras på den virtuella datorn eller nivån för gästoperativsystemet. Redundansväxlingen hanteras med hjälp av ytterligare programvara som inte är en del av programmet.

Med den här metoden kan programmet distribueras i ett moln och dess virtuella datorn replikeras till andra molnet. Om en redundans initieras, måste sekundärt virtuella datorer behöver vara påslagna i andra moln. Redundans skapar diskar för virtuella datorer och bifogar dem i vissa situationer. Den här processen kan ta lång tid att slutföra, särskilt i ett program som kräver en specifik Start-sekvens med flera nivåer. Det kan också finnas steg som måste köras innan programmet är redo att börja servar förfrågningar.

![Replikering manuell redundans](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Nedtid i minuter
 - RPO: Variabeln dataförlust (beroende på replikeringsfrekvensen)
 - Topologi för distribution: Aktiv/passiv vänteläge
 
### <a name="high-availabilityautomatic-failover"></a>Hög tillgänglighet/automatisk växling vid fel

För program där ditt företag kan tolerera fel på några sekunder eller minuter för driftstopp och minimal dataförlust, behöver du överväga en konfiguration med hög tillgänglighet. Program med hög tillgänglighet är utformade för att snabbt och automatiskt återställa från fel. För lokala maskinvarufel implementerar hög tillgänglighet i Azure Stack-infrastruktur i det fysiska nätverket med hjälp av två överst växlar rack. För beräkning på fel använder flera noder i en skalningsenhet i Azure Stack. På VM-nivå kan du använda skalningsuppsättningar i kombination med feldomäner för att säkerställa nodfel inte ta bort programmet.

I kombination med skalningsuppsättningar måste ditt program har inbyggt stöd för hög tillgänglighet eller stöd för användning av klusterprogram. Till exempel stöder Microsoft SQL Server hög tillgänglighet internt för databaser med synkront genomförande. Men om du bara stöder asynkron replikering, kan sedan det data gå förlorade. Program kan också distribueras till ett redundanskluster där klusterprogrammet hanterar automatisk redundans för programmet.

Med den här metoden kan programmet endast är aktiv i ett moln, men programvaran distribueras till flera moln. De andra molnen är i vänteläge redo att starta programmet när redundansen utlöses.

 - RTO: Nedtid mätt i sekunder
 - RPO: Minimal dataförlust
 - Topologi för distribution: Aktiv/aktiv vänteläge

### <a name="fault-tolerance"></a>Feltolerans

Azure Stack fysiska redundans och infrastruktur tjänsttillgänglighet bara skydda mot maskinvara nivå fel/fel sådana en disk, strömförsörjning, nätverksport eller noden. Men om ditt program alltid måste vara tillgänglig och kan aldrig att förlora några data, måste implementera feltolerans internt i ditt program eller använda ytterligare programvara för att aktivera feltolerans.

Först måste du se till att programmet virtuella datorerna distribueras med skalan anger för att skydda mot fel på noden på servernivå. För att skydda mot molnet försätts i offlineläge, måste samma program redan distribueras till ett annat moln, så att den kan fortsätta att betjäna förfrågningar utan avbrott. Den här modellen kallas vanligtvis en aktiv-aktiv-distribution.

Tänk på att varje Azure Stack-molnet är oberoende av varandra, så molnen betraktas alltid active ur en infrastruktur. I det här fallet distribueras flera aktiva instanser av programmet till ett eller flera aktiva moln.

 - RTO: Utan avbrott
 - RPO: Ingen dataförlust
 - Topologi för distribution: Aktiv/aktiv

### <a name="no-recovery"></a>Inte kan återställas

Vissa program i din miljö kanske inte behöver skydd mot oplanerade avbrott eller dataförlust. Till exempel virtuella datorer som används för utveckling och testning vanligtvis behöver du inte återställas. Det är ditt beslut att göra utan skydd för ett program eller en specifik virtuell dator. Azure Stack erbjuder inte säkerhetskopiering eller replikering av virtuella datorer från den underliggande infrastrukturen. Liknar Azure, behöver du anmäla sig till skydd för varje virtuell dator i var och en av dina prenumerationer.

 - RTO: Ett oåterkalleligt
 - RPO: Fullständig dataförlust

## <a name="recommended-topologies"></a>Rekommenderade topologier

Viktigt att tänka på för Azure Stack-distributionen:

|     | Rekommendation | Kommentarer |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopiering/återställning virtuella datorer till en extern mål för säkerhetskopian som redan har distribuerats i ditt datacenter | Rekommenderas | Dra nytta av befintliga infrastruktur för säkerhetskopiering och operativa kunskaper. Se till att ändra storlek på infrastruktur för säkerhetskopiering så att den är redo att skydda ytterligare VM-instanser. Kontrollera att en infrastruktur för säkerhetskopiering inte är i närheten källan. Du kan återställa virtuella datorer till Azure Stack-källan till en sekundär Azure Stack-instans eller till Azure. |
| Säkerhetskopiering/återställning virtuella datorer till en extern säkerhetskopieringsmål som är dedikerad till Azure Stack | Rekommenderas | Du kan köpa ny infrastruktur för säkerhetskopiering eller etablera en dedikerad infrastruktur för säkerhetskopiering för Azure Stack. Kontrollera att en infrastruktur för säkerhetskopiering inte är i närheten källan. Du kan återställa virtuella datorer till Azure Stack-källan till en sekundär Azure Stack-instans eller till Azure. |
| Virtuella datorer direkt till global Azure eller en betrodd tjänstleverantör för säkerhetskopiering/återställning | Rekommenderas | Så länge som du kan uppfylla din datasekretess och regelkrav, kan du lagra säkerhetskopiorna i globala Azure eller en betrodd tjänstleverantör. Vi rekommenderar kör tjänstleverantören också Azure Stack så att du får konsekvens i operational upplevelse när du återställer. |
| Replikera/redundans-datorer till en separat instans av Azure Stack | Rekommenderas | I fallet redundans behöver ha en andra Azure Stack-molnet fullt fungerande för att undvika utökade nedtid. |
| Replikera/redundans virtuella datorer direkt till Azure eller en betrodd tjänstleverantör | Rekommenderas | Så länge som du kan uppfylla din datasekretess och regelkrav, kan du replikera dina data till globala Azure eller en betrodd tjänstleverantör. Vi rekommenderar kör tjänstleverantören också Azure Stack så att du får konsekvens i operational upplevelse efter en redundansväxling. |
| Distribuera mål för säkerhetskopian i samma Azure Stack-moln med dina programdata | Rekommenderas inte | Undvik att lagra säkerhetskopior inom samma Azure Stack-moln. Oplanerade driftstopp för molnet kan du hålla dig från dina primära data och säkerhetskopiera data. Om du väljer att distribuera ett säkerhetskopieringsmål som en virtuell installation (för optimering för säkerhetskopiering och återställning), måste du kontrollera att alla data kopieras kontinuerligt till en extern plats för säkerhetskopia. |
| Distribuera fysisk säkerhetskopiering installation i samma rack där Azure Stack-lösningen har installerats | Stöds inte | Du kan inte vid denna tidpunkt, ansluta andra enheter längst upp i rack växlar som inte är en del av den ursprungliga lösningen. |

## <a name="next-steps"></a>Nästa steg

Den här artikeln anges allmänna riktlinjer för att skydda användare virtuella datorer som distribueras på Azure Stack. Information om hur du använder Azure-tjänster för att skydda användarnas VM: ar finns:

 - [Använda Azure Backup för att säkerhetskopiera filer och program på Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server-stöd för Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery-support för Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Läs mer om partnerprodukter som erbjuder skydd av virtuell dator på Azure Stack ”[skyddar program och data i Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)”.
