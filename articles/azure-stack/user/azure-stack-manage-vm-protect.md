---
title: Skydda virtuella datorer som distribueras på Azure-stacken | Microsoft Docs
description: Riktlinjer för hur du skyddar virtuella datorer distribueras på Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 02get-started-article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: hector.linares
ms.openlocfilehash: 0e74c6af36130d206456634548f452a1f1a2d4af
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Skydda virtuella datorer distribueras på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Den här artikeln beskriver riktlinjer för hur du skyddar användare virtuella datorer som distribueras på Azure-stacken.

Du måste implementera en plan för säkerhetskopiering återställning eller katastrofåterställning för dina data och program för att skydda mot dataförlust och oplanerade driftavbrott. Den här planen är unik för varje program men följer ett ramverk som upprättas av din organisations omfattande affärskontinuitet och haveriberedskap (BC/DR). Allmänna mönster och metoder för program tillgänglighet och flexibilitet finns [flexibel när program utformas för Azure](https://docs.microsoft.com/azure/architecture/resiliency) i Azures arkitektur Center.

## <a name="azure-stack-infrastructure-recovery"></a>Återställning av Azure Stack-infrastruktur

Användare behöver att skydda deras virtuella datorer separat från Azure-stacken infrastrukturtjänster.

Om Azure-stacken molnet är offline under en längre tid eller permanent oåterkalleligt, måste ha en plan för att hålla din programunderhåll användarförfrågningar med minimal avbrottstid. Operatorn Azure Stack molnet ansvarar för att förbereda en återställningsplan för underliggande Azure Stack-infrastruktur och tjänster. Mer information finns i artikeln [återställa från oåterkallelig dataförlust](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data). 

Återställningsplan för Azure-stacken infrastrukturtjänster inkluderar inte återställning av användarnas VM, storage-konton eller databaser. Du är ansvarig för att implementera en återställningsplan för dina program och data som program-ägare.
 
## <a name="sourcetarget-combinations"></a>Källan/målet kombinationer

Varje Azure-stacken moln har distribuerats i ett datacenter. En separat miljö krävs så att du kan återställa dina program. Miljön kan vara ett separat Azure Stack-moln i olika datacenter eller det offentliga Azure-molnet. Dina data suveränitet och datasekretesskrav avgör Återställningsmiljö för ditt program. När du aktiverar skyddet för varje program har du möjlighet att välja en specifik återställningsalternativ för varje kriterium. Du kan ha program i en prenumeration säkerhetskopiera data till en annan datacenter. I en annan prenumeration kan du replikera data till det offentliga Azure-molnet. 
 
Planera din strategi för säkerhetskopiering återställning och katastrofåterställning för varje program för att fastställa målet för varje program. Detta hjälper organisationen korrekt storlek lagringskapacitet krävs lokalt och projektet förbrukning i det offentliga molnet. 

|  | Global Azure | Azure stacken har distribuerats till CSP-datacentret och drivas av Kryptografiprovider | Azure stacken har distribuerats till kunden datacenter och drivas av kunden |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure stacken har distribuerats till CSP-datacentret och drivas av Kryptografiprovider** | Virtuella datorer som användaren har distribuerats till CSP: N drivas Azure stacken. Användarnas VM återställts från säkerhetskopian eller växlar över direkt till Azure. | CSP fungerar de primära och sekundära instanserna av Azure-stacken i sina egna datacenter. Användarnas VM återställas eller växlar över mellan två Azure Stack-instanser. | CSP fungerar Azure-stacken i den primära platsen. Kundens datacenter är mål för återställning eller växling vid fel. |
| **Azure stacken har distribuerats till kunden datacenter och drivas av kunden** | Virtuella datorer som användaren har distribuerats till kunden drivas Azure stacken. Användarnas VM återställts från säkerhetskopian eller växlar över direkt till Azure. | Kunden fungerar de primära och sekundära instanserna av Azure-stacken i sina egna datacenter. Användarnas VM återställas eller växlar över mellan två Azure Stack-instanser. | Kunden fungerar Azure-stacken i den primära platsen. CSP-datacenter är mål för återställning eller växling vid fel. |

![Kombinationer av käll-mål](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Mål för återställning av program

Du måste avgöra hur mycket driftavbrott och dataförlust som din organisation kan tolerera för varje program för att avgöra den bästa metoden för att skydda dina program på Azure-stacken. Mängden downtown och mängden data går förlorade värden är nödvändiga för att identifiera för att utveckla och implementera en återställningsplan som minimerar påverkan på din organisation. Överväg att för varje program:  
 - **Återställning tid mål för Återställningstid**  
Återställningstidsmålet är den maximala acceptabla tid som ett program kan användas när en incident. Om din RTO är 90 minuter måste du kunna återställa programmet till ett fungerande tillstånd inom 90 minuter från starten av ett haveri. Om du har en låga RTO, kan du behålla en andra distribution som körs kontinuerligt i vänteläge för att skydda mot ett regionalt strömavbrott.
 - **Mål för återställningspunkt (RPO)**  
Återställningspunktmålet är maximal varaktighet för förlust av data som tillåts under en katastrofåterställning. Om du till exempel lagrar data i en enda databas, utan replikering till andra databaser, och säkerhetskopierar varje timme kan du förlora upp till en timme med data.
 
RTO och RPO handlar om affärsbehov. Utföra en riskbedömning för att definiera programmets RTO och Återställningspunktmål. En annan vanliga mått är **tid att återställa** (MTTR), vilket är den genomsnittliga tiden som det tar för att återställa programmet efter ett fel. MTTR är ett empiriskt fakta kring ett system. Om MTTR är längre än RTO så skapar fel i systemet oacceptabla affärsstörningar, eftersom det inte går att återställa systemet inom definierad RTO.

### <a name="backup-restore"></a>Backup-restore

De vanligaste är skydd för VM-baserade program att använda ett säkerhetskopieringsprogram. Säkerhetskopiering av en virtuell dator vanligtvis innehåller operativsystemet, konfiguration, binärfiler och programdata. Säkerhetskopieringar skapas en ögonblicksbild av volymer, diskar eller hela den virtuella datorn. Med Azure Stack har möjlighet att säkerhetskopiera från inom ramen för gästoperativsystemet eller från Azure Stack-lagring och beräkna API: er. Azure-stacken stöder inte med säkerhetskopior på hypervisor-nivå. 
 
![Backup-restor](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)
 
Återställa programmet kräver att återställa en eller flera virtuella datorer till samma moln eller till en ny moln. Du kan rikta ett moln i ditt datacenter eller det offentliga molnet. Vilka molnappar som du riktar är helt inom din kontroll och baseras på dina data sekretess och suveränitet krav. 
 
 - Återställningstidsmålet: Driftstopp mäts i timmar 
 - Återställningspunktmålet: Variabel dataförlust (beroende på frekvensen för säkerhetskopiering)
 - Topologi för distribution: aktiv/passiv 

#### <a name="planning-your-backup-strategy"></a>Planera din strategi för säkerhetskopiering

Planera din strategi för säkerhetskopiering och definiera skalningskrav börjar du med är lika med antalet förekomster av en virtuell dator som skyddas. Säkerhetskopiera alla virtuella datorer på alla servrar i en miljö är ett mycket vanligt strategi. Med Azure-stacken finns det dock vissa virtuella datorer som behöver säkerhetskopieras. Till exempel anses virtuella datorer i en skaluppsättning tillfälliga resurser som kan hämtas och ibland gå utan föregående meddelande. Beständiga data som måste skyddas lagras i en separat databas som en databas eller objektet store. Viktigt att tänka på för att säkerhetskopiera virtuella datorer på Azure Stack:

 - **Kategorisering**
    - Överväg att en modell där användarna välja att säkerhetskopiering.
    - Definiera en återställning SLA baserat på prioriteten för program eller påverkan på verksamheten.
 - **Skalning**
    - Tänk successiva säkerhetskopieringar när på-boarding ett stort antal nya virtuella datorer (om säkerhetskopiering krävs).
    - Utvärdera säkerhetskopiering produkter som effektivt kan samla in och överföra säkerhetskopierade data för att minimera Resursinnehåll i lösningen.
    - Utvärdera säkerhetskopiering produkter som effektivt lagrar säkerhetskopierade data med hjälp av inkrementell eller differentiella säkerhetskopieringar för att minimera behovet av att dra och fullständiga säkerhetskopieringar över alla virtuella datorer i miljön.
 - **Återställ**
    - Säkerhetskopiering produkter kan återställa virtuella diskar, programdata i en befintlig virtuell dator eller hela Virtuella datorresursen och associerade virtuella diskar. Vilka återställning schema som du behöver beror på hur du planerar att återställa programmet och påverkar din tid till återställning av programmet. Exempelvis kan det vara lättare att distribuera om SQLServer från en mall och sedan återställa databaser i stället för att återställa hela VM eller uppsättning virtuella datorer.


### <a name="replicationmanual-failover"></a>Replikering/manuell redundans

En alternativ metod för att stödja hög tillgänglighet är att replikera dina program på virtuella datorer till ett annat moln och förlitar sig på en manuellt utlösta växling vid fel. Replikering av operativsystemet, binärfiler och programdata kan utföras på den virtuella datorn nivå eller gäst OS. Hantera redundans med hjälp av ytterligare programvara som är separat från programmet.
 
Programmet har endast distribuerats i ett moln med den här metoden. Den virtuella datorn replikeras sedan till andra moln i taget för ditt datacenter eller till det offentliga molnet. Om en redundansväxling initieras sekundärt virtuella datorer behöver vara påslagna i andra molnet. I vissa fall redundans skapar de virtuella datorerna och kopplar dem till diskarna. Den här processen kan ta lång tid att slutföra, särskilt med ett program med flera nivåer med en specifik uppstart sekvens. Det kan finnas ytterligare steg som måste utföras innan programmet är redo att börja betjäna förfrågningar.

![Replikering manuell redundans](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - Återställningstidsmålet: Driftstopp mätt i minuter 
 - Återställningspunktmålet: Variabel dataförlust (beroende på replikeringsfrekvens)
 - Topologi för distribution: aktiv/passiv vänteläge
 
### <a name="high-availabilityautomatic-failover"></a>Hög tillgänglighet/automatisk redundans

För program där din verksamhet kan tolerera några sekunder eller minuter driftstopp och minimala dataförlust, behöver du en konfiguration med hög tillgänglighet. Program med hög tillgänglighet är utformade för att snabbt och automatiskt återställa från fel. För lokala hårdvarufel implementerar Azure Stack-infrastruktur med hög tillgänglighet i det fysiska nätverket med hjälp av två överkant rack växlar. För beräkning nivån fel använder Azure-stacken flera noder i en skalningsenhet. På VM-nivå kan du använda skalningsuppsättningar i kombination med feldomäner så nodfel inte börjar ned ditt program. 
 
I kombination med skaluppsättningar för behöver ditt program internt stöd för hög tillgänglighet eller stöder användning av kluster för programvara. Till exempel stöder Microsoft SQL Server hög tillgänglighet internt för databaser som använder läget för synkront genomförande. Men om du stöder bara asynkron replikering, kan sedan det data gå förlorade. Program kan också distribueras till ett kluster där klusterprogrammet hanterar automatisk redundans för programmet. 
 
Med den här metoden programmet är endast aktiv i ett moln, men programvaran distribueras till flera moln. De andra molnen är i vänteläge redo att starta programmet när växling vid fel utlöses.

 - Återställningstidsmålet: Driftstopp mätt i sekunder 
 - Återställningspunktmål: Minimal dataförlust
 - Topologi för distribution: aktiva vänteläge

### <a name="fault-tolerance"></a>Feltolerans

Azure-stacken fysiska redundans och infrastruktur tjänsttillgänglighet bara skydda från nivån hårdvarufel som en disk som misslyckats misslyckades strömförsörjning, misslyckades nätverksport eller misslyckades nod. Men om ditt program måste alltid vara tillgängliga och kan aldrig att förlora data, måste du implementerar feltolerans internt i ditt program eller aktivera feltolerans med ytterligare programvara. 
 
Först måste du se till att programmet virtuella datorer distribueras med skalan anger för att skydda mot nivån fel. För att skydda mot molnet försätts i offlineläge måste samma program redan ha distribuerats till ett annat moln, så att den kan fortsätta att betjäna förfrågningar utan avbrott. Detta kallas vanligtvis till en aktiv-aktiv distribution.
 
Kom ihåg att varje moln för Azure-stacken är oberoende av varandra, så molnen betraktas alltid active ur en infrastruktur. I det här fallet har flera aktiva instanser av programmet distribuerats till en eller flera aktiva moln. 
 
 - Återställningstidsmålet: Utan avbrott
 - Återställningspunktmålet: Ingen dataförlust
 - Topologi för distribution: aktiva

### <a name="no-recovery"></a>Ingen återställning

Vissa program i din miljö kanske inte behöver skydd mot oplanerade avbrott eller dataförlust. Till exempel virtuella datorer som används för utveckling och testning normalt behöver du inte kan återställas. Det är ditt beslut att göra utan skydd för ett program eller en specifik VM. Azure-stacken erbjuder inte säkerhetskopiering eller replikering av virtuella datorer från den underliggande infrastrukturen. Liknande till Azure, behöver du delta i skyddet för varje virtuell dator i var och en av dina prenumerationer.  
 
 - Återställningstidsmålet: ett oåterkalleligt 
 - Återställningspunktmål: Fullständig dataförlust
 
## <a name="recommended-topologies"></a>Rekommenderade topologier 

Viktigt att tänka på för Azure Stack-distribution:

|     | Rekommendation | Kommentarer |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Säkerhetskopiering/återställning virtuella datorer till en extern mål för säkerhetskopian som redan har distribuerats i ditt datacenter | Rekommenderas | Dra nytta av befintliga infrastruktur för säkerhetskopiering och operativa kunskaper. Se till att storleken infrastrukturen för säkerhetskopiering så att den är redo att skydda ytterligare VM-instanser. Se till att infrastrukturen för säkerhetskopiering inte finns i närheten av datakällan. Du kan återställa virtuella datorer till Azure-stacken källan till en sekundär Azure Stack-instans eller Azure. |
| Säkerhetskopiering/återställning virtuella datorer till en extern mål för säkerhetskopian dedikerade till Azure-stacken | Rekommenderas | Du kan köpa nya säkerhetskopiering infrastruktur eller etablera dedikerad säkerhetskopiering infrastruktur för Azure-stacken. Se till att infrastrukturen för säkerhetskopiering inte finns i närheten av datakällan. Du kan återställa virtuella datorer till Azure-stacken källan till en sekundär Azure Stack-instans eller Azure. |
| Säkerhetskopiera/Återställ virtuella datorer direkt i globala Azure eller en betrodd-leverantör | Rekommenderas | Så länge som du kan uppfylla datasekretess- och regelkrav, kan du lagra säkerhetskopiorna i globala Azure eller en betrodd service provider. Helst kör tjänstleverantör också Azure Stack så att du får konsekvens i operativa upplevelsen när du återställer. |
| Replikera/redundans virtuella datorer till en separat instans av Azure-stacken | Rekommenderas | I fallet med växling vid fel behöver du ha ett andra moln för Azure-stacken fungerar så att du kan undvika utökade driftavbrott. |
| Replikera/redundans virtuella datorer direkt till Azure eller en betrodd-leverantör | Rekommenderas | Så länge som du kan uppfylla datasekretess- och regelkrav, kan du replikera data till globala Azure eller en betrodd tjänstprovider. Helst kör tjänstleverantör också Azure Stack så att du får konsekvens i operativ erfarenhet efter växling vid fel. |
| Distribuera mål för säkerhetskopian i samma Azure Stack-moln med dina programdata | Rekommenderas inte | Undvik att lagra säkerhetskopior inom samma moln för Azure-stacken. Oplanerat stillestånd i molnet kan du hålla dig från dina primära data och säkerhetskopiera data. Om du väljer att distribuera ett mål som virtuell utrustning (för optimering för säkerhetskopiering och återställning), måste du kontrollera att alla data kontinuerligt kopieras till en extern plats för säkerhetskopiering. |
| Distribuera fysiska säkerhetskopiering installation i samma rack där lösningen Azure-stacken är installerad | Stöds inte | Vid denna tidpunkt, kan du ansluta andra enheter överst i rack växlar som inte är del av den ursprungliga lösningen. |
 
## <a name="next-steps"></a>Nästa steg 

I den här artikeln beskrivs vi riktlinjer för hur du skyddar användare virtuella datorer som distribueras på Azure-stacken. Mer information om hur du skyddar dina virtuella datorer med hjälp av Azure-tjänster finns:
 - [Azure Backup Server-stöd för Azure-stacken](https://docs.microsoft.com/en-us/azure/backup/ ) 
 - [Azure Site Recovery-stöd för Azure-stacken](https://docs.microsoft.com/en-us/azure/site-recovery/)  
 
Mer information om de partner-produkter som ger skydd för Virtuella Azure-stacken avser ”[skyddar program och data på Azure-stacken](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)”.
