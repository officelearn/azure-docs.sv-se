---
title: Hyper-V-arkitektur för haveriberedskap i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid distribution av haveriberedskap för lokala virtuella hyper-virtuella datorer (utan VMM) till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082671"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Haveriberedskapsarkitektur för Hyper-V till Azure


I den här artikeln beskrivs arkitekturen och processerna som används när du replikerar, växlar över och återställer virtuella Hyper-V-datorer mellan lokala Hyper-V-värdar och Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)

Hyper-V-värdar kan eventuellt hanteras i privata moln (Virtual Machine Manager) i System Center Virtual Machine Manager.Hyper-V hosts can optionally be managed in System Center Virtual Machine Manager (VMM) private clouds.



## <a name="architectural-components---hyper-v-without-vmm"></a>Arkitektoniska komponenter - Hyper-V utan VMM

Följande tabell och grafik ger en vy på hög nivå av de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar inte hanteras av VMM.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure-lagringskonto och Azure-nätverk. | Replikerade data från lokala VM-arbetsbelastningar lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade arbetsbelastningsdata när redundans från din lokala plats inträffar.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Hyper-V** | Under distributionen av platsåterställning samlar du hyper-V-värdar och kluster till Hyper-V-platser. Du installerar Azure Site Recovery Provider and Recovery Services-agenten på varje fristående Hyper-V-värd eller på varje Hyper-V-klusternod. | Providern samordnar replikeringen med Site Recovery-tjänsten via Internet. Recovery Services-agenten hanterar datareplikeringen.<br/><br/> Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.
**Virtuella Hyper-V-datorer** | En eller flera virtuella datorer som körs på Hyper-V. | Ingenting behöver uttryckligen installeras på virtuella datorer.


**Hyper-V till Azure-arkitektur (utan VMM)**

![Arkitektur](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Arkitektoniska komponenter - Hyper-V med VMM

Följande tabell och grafik ger en vy på hög nivå av de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar hanteras i VMM-moln.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure-lagringskonto och Azure-nätverk. | Replikerade data från lokala VM-arbetsbelastningar lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade data när redundans från din lokala plats inträffar.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**VMM-server** | VMM-servern har ett eller flera moln som innehåller Hyper-V-värdar. | Du installerar site recovery provider på VMM-servern, för att dirigera replikering med Site Recovery och registrera servern i Recovery Services-valvet.
**Hyper-V-värd** | En eller flera Hyper-V-värdar eller Hyper-V-kluster som hanteras av VMM. |  Du installerar Recovery Services-agenten på varje Hyper-V-värd- eller klusternod.
**Virtuella Hyper-V-datorer** | En eller flera virtuella datorer som körs på en Hyper-V-värdserver. | Du behöver inte installera något på de virtuella datorerna.
**Nätverk** | Logiska och virtuella datornätverk som konfigurerats på VMM-servern. VM-nätverket ska länkas till ett logiskt nätverk som är associerat med molnet. | VM-nätverk mappas till virtuella Azure-nätverk. När virtuella Azure-datorer skapas efter redundans läggs de till i Azure-nätverket som är mappat till VM-nätverket.

**Hyper-V till Azure-arkitektur (med VMM)**

![Komponenter](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replikeringsprocessen

![Hyper-V till Azure-replikering](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikerings- och återställningsprocess**


### <a name="enable-protection"></a>Aktivera skydd

1. När du har aktiverat skydd för en virtuell Hyper-V-dator i Azure Portal eller lokalt startar **Aktivera skydd**.
2. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering med de inställningar som du har konfigurerat.
3. Jobbet startar den initiala replikeringen genom att aktivera metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig VM-replikering och skicka de virtuella datorernas diskar till Azure.
4. Du kan övervaka jobbet på fliken **Jobb.**      ![Jobblista](media/hyper-v-azure-architecture/image1.png) ![Aktivera skydd detaljgranskning](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Inledande datareplikering

1. När den första replikeringen utlöses tas en ögonblicksbild av virtuella datorer med [Hyper-V-virtuell](https://technet.microsoft.com/library/dd560637.aspx) dator.
2. Virtuella hårddiskar på den virtuella datorn replikeras en efter en, tills de alla kopieras till Azure. Detta kan ta ett tag, beroende på den virtuella datorns storlek och nätverksbandbredd. [Läs om hur du](https://support.microsoft.com/kb/3056159) ökar nätverksbandbredden.
3. Om diskändringar inträffar medan den första replikeringen pågår spårar Hyper-V Replica Replication Tracker ändringarna som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till sekundär lagring. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


### <a name="finalize-protection-process"></a>Slutföra skyddsprocessen

1. När den första replikeringen är klar körs slutför skyddet för jobbet **för den virtuella datorn.** Den konfigurerar nätverksinställningar och andra inställningar efter replikering, så att den virtuella datorn är skyddad.
2. I det här skedet kan du kontrollera vm-inställningarna för att se till att den är redo för redundans. Du kan köra en haveriberedskapsövning (test redundans) för den virtuella datorn för att kontrollera att den växlar över som förväntat. 


## <a name="delta-replication"></a>Deltareplikering

1. Efter den första replikeringen börjar deltareplikeringen, i enlighet med replikeringsprincipen.
2. Hyper-V Replica Replication Tracker spårar ändringar på en virtuell hårddisk som HRL-filer. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
3. Loggen skickas till kundens lagringskonto. När en logg är under överföring till Azure spåras ändringarna i den primära disken i en annan loggfil i samma mapp.
4. Under inledande och delta-replikering kan du övervaka den virtuella datorn i Azure-portalen.

### <a name="resynchronization-process"></a>Omsynkroniseringsprocess

1. Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så markeras en virtuell dator för omsynkronisering.
    - Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering.
    -  Som standard är omsynkronisering schemalagd att köras automatiskt utanför kontorstid.
1.  Omsynkronisering skickar endast deltadata.
    - Det minimerar mängden data som skickas genom att beräkna kontrollsummor för käll- och mål-virtuella datorer.
    - Den använder en fast block chunking algoritm där käll- och målfiler är indelade i fasta segment.
    - Kontrollsummar för varje segment genereras. Dessa jämförs för att avgöra vilka block från källan som måste tillämpas på målet.
2. När omsynkroniseringen är klar ska den normala deltareplikeringen återupptas.
3. Om du inte vill vänta på standardresynkronisering utanför timmar kan du synkronisera om en virtuell dator manuellt. Till exempel om ett avbrott inträffar. För att göra detta, i Azure-portalen, välj den virtuella datorn > **synkronisera**om .

    ![Manuell omsynkronisering](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Återförsöksprocess

Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Återförsök klassificeras enligt beskrivningen i tabellen.

**Kategori** | **Detaljer**
--- | ---
**Oåterkalleliga fel** | Inga nya försök görs. Status för den virtuella datorn är **kritisk**, och administratören måste ingripa.<br/><br/> Exempel på dessa fel är en trasig VHD-kedja, ett ogiltigt tillstånd för den virtuella repliken, nätverksautentiseringsfel, auktoriseringsfel och vm hittades inte fel (för fristående Hyper-V-servrar.
**Återkalleliga fel** | Återförsök sker varje replikeringsintervall, med ett exponentiellt undantagsläge som ökar återförsöksintervallet från det första försökets start med 1, 2, 4, 8 och 10 minuter. Om felet kvarstår försöker du var 30: e minut. Exempel på dessa är nätverksfel, diskfel och tillstånd för lågt minne.



## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad redundansväxling från lokala Hyper-V virtuella datorer till Azure. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. Kör en oplanerad redundans om din primära webbplats inte är tillgänglig.
2. Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
3. Du kör en redundans. När den första fasen av redundansen är klar bör du kunna se de skapade virtuella replikerna i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
4. Du genomför sedan redundansen för att börja komma åt arbetsbelastningen från repliken Azure VM.

När den lokala infrastrukturen är igång igen kan du återställa den. Återställning sker i tre steg:

1. Starta en planerad redundans från Azure till den lokala platsen:
    - **Minimera stilleståndstiden:** Om du använder det här alternativet Site Recovery synkroniserar data före redundans. Den söker efter ändrade datablock och hämtar dem till den lokala webbplatsen, medan Den virtuella Azure-datorn fortsätter att köras, vilket minimerar driftstopp. När du anger manuellt att redundansen ska slutföras stängs Azure-virtuella datorn av, eventuella slutliga deltaändringar kopieras och redundansen startar.
    - **Fullständig nedladdning:** Med det här alternativet synkroniseras data under redundans. Det här alternativet hämtar hela disken. Det går snabbare eftersom inga kontrollsummar beräknas, men det finns mer driftstopp. Använd det här alternativet om du har kört repliken Azure virtuella datorer under en tid, eller om den lokala virtuella datorn har tagits bort.
    - **Skapa virtuell dator:** Du kan välja att växla tillbaka till samma virtuella dator eller till en alternativ virtuell dator. Du kan ange att site recovery ska skapa den virtuella datorn om den inte redan finns.

2. När den första synkroniseringen är klar väljer du att slutföra redundansen. När den är klar kan du logga in på den lokala virtuella datorn för att kontrollera att allt fungerar som förväntat. I Azure-portalen kan du se att virtuella Azure-datorer har stoppats.
3.  Sedan genomför du redundansen för att slutföra och börjar komma åt arbetsbelastningen från den lokala virtuella datorn igen.
4. När arbetsbelastningarna har misslyckats kan du aktivera omvänd replikering, så att de lokala virtuella datorerna replikeras till Azure igen.



## <a name="next-steps"></a>Nästa steg


Följ [den här självstudien](tutorial-prepare-azure.md) för att komma igång med Hyper-V till Azure-replikering.


