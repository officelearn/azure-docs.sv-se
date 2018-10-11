---
title: Hyper-V till Azure-replikeringsarkitektur i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att replikera lokala virtuella datorer med Hyper-V (utan VMM) till Azure med tjänsten Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 49059415c5f96eeb4dd871e7d2c8ae4ab9b2c12e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078366"
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Hyper-V till Azure-replikering-arkitektur


Den här artikeln beskrivs arkitekturen och processer som används när du replikera, redundansväxla och återställa Hyper-V-datorer (VM) mellan lokala Hyper-V-värdar och Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

Hyper-V-värdar kan du kan också hanteras i privata moln i System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Arkitekturkomponenter - Hyper-V utan VMM

Följande tabell och bild ger en översikt över de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar inte hanteras av VMM.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure storage-konto och Azure-nätverk. | Replikerade data från en lokal VM-arbetsbelastningar lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade arbetsbelastningsdata när det uppstår redundans från din lokala plats.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Hyper-V** | Under distributionen av Site Recovery måste du samla in Hyper-V-värdar och kluster i Hyper-V-platser. Du installerar Azure Site Recovery-providern och Recovery Services-agenten på varje fristående Hyper-V-värd eller på varje Hyper-V-klusternod. | Providern samordnar replikeringen med Site Recovery-tjänsten via Internet. Recovery Services-agenten hanterar datareplikeringen.<br/><br/> Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.
**Virtuella Hyper-V-datorer** | En eller flera virtuella datorer som körs på Hyper-V. | Inget behöver uttryckligen installeras på virtuella datorer.


**Hyper-V till Azure-arkitektur (utan VMM)**

![Arkitektur](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Arkitekturkomponenter - Hyper-V med VMM

Följande tabell och bild ger en översikt över de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar hanteras i VMM-moln.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure storage-konto och Azure-nätverk. | Replikerade data från en lokal VM-arbetsbelastningar lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerad data när uppstår redundans från din lokala plats.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**VMM-server** | VMM-servern har ett eller flera moln som innehåller Hyper-V-värdar. | Du installerar Site Recovery-providern på VMM-servern att samordna replikeringen med Site Recovery och registrera servern i Recovery Services-valvet.
**Hyper-V-värd** | En eller flera Hyper-V-värdar eller Hyper-V-kluster som hanteras av VMM. |  Du kan installera Recovery Services-agenten på varje Hyper-V-värd eller kluster-nod.
**Virtuella Hyper-V-datorer** | En eller flera virtuella datorer som körs på en Hyper-V-värdserver. | Du behöver inte installera något på de virtuella datorerna.
**Nätverk** | Logiska och virtuella datornätverk som konfigurerats på VMM-servern. Det Virtuella datornätverket bör vara kopplat till ett logiskt nätverk som är associerat med molnet. | Virtuella datornätverk som mappas till virtuella Azure-nätverk. När virtuella Azure-datorerna skapats efter redundansen, läggs de till Azure-nätverket som är mappad till det Virtuella datornätverket.

**Hyper-V till Azure-arkitektur (med VMM)**

![Komponenter](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replikeringsprocessen

![Hyper-V till Azure replikering](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikering och återställningsprocess**


### <a name="enable-protection"></a>Aktivera skydd

1. När du har aktiverat skydd för en virtuell Hyper-V-dator i Azure Portal eller lokalt startar **Aktivera skydd**.
2. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering med de inställningar som du har konfigurerat.
3. Jobbet startar den initiala replikeringen genom att aktivera metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig VM-replikering och skicka de virtuella datorernas diskar till Azure.
4. Du kan övervaka jobbet på fliken **Jobs** (Jobb).      ![Lista över jobb](media/hyper-v-azure-architecture/image1.png) ![Detaljnivå för Aktivera skydd](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Inledande replikering

1. När den inledande replikeringen utlöses en [Hyper-V VM-ögonblicksbild](https://technet.microsoft.com/library/dd560637.aspx) ögonblicksbilden tas.
2. Virtuella hårddiskar på den virtuella datorn är replikeras en i taget, tills alla har kopierats till Azure. Detta kan ta en stund, beroende på VM-storlek och nätverkets bandbredd. [Lär dig hur](https://support.microsoft.com/kb/3056159) att öka bandbredd i nätverket.
3. Om diskändringar inträffar under den inledande replikeringen pågår, spårar ändringarna i den spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad hrl-fil som skickas till sekundär lagring. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


### <a name="finalize-protection-process"></a>Slutför processen för skydd

1. När den inledande replikeringen är klar i **Slutför skydd på den virtuella datorn** jobbet körs. Den konfigurerar nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad.
2. I det här skedet kan du kontrollera inställningarna för virtuella datorer och kontrollera att den är redo för redundans. Du kan köra ett programåterställningstest (testa redundans) för den virtuella datorn att kontrollera att den inte över som förväntat. 


## <a name="delta-replication"></a>Deltareplikering

1. Efter den första replikeringen börjar deltareplikering enligt replikeringsprincipen.
2. Den spårningsverktyget för Hyper-V-replikering spårar ändringar i en virtuell hårddisk som .hrl-filer. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
3. Loggen skickas till kundens lagringskonto. När en logg är på väg till Azure, spåras ändringar i den primära disken i en loggfil i samma mapp.
4. Under initial och deltareplikering replikering, kan du övervaka den virtuella datorn i Azure-portalen.

### <a name="resynchronization-process"></a>Omsynkroniseringen process

1. Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så markeras en virtuell dator för omsynkronisering.
    - Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering.
    -  Omsynkroniseringen schemaläggs som standard automatisk körning utanför kontorstid.
1.  Omsynkroniseringen skickar delta-data.
    - Den minimerar mängden data som skickas av kontrollsummor beräknas för de virtuella datorerna för källa och mål.
    - Den använder en delningsalgoritm med fasta block där käll-och målfiler delas in i fasta segment.
    - Kontrollsummor skapas för varje segment skapas. Dessa jämförs för att avgöra vilka block från källan som ska tillämpas till målet.
2. När omsynkroniseringen är klar ska den normala deltareplikeringen återupptas.
3. Om du inte vill vänta tills omsynkroniseringen är standard utanför timmar, kan du synkronisera om en virtuell dator manuellt. Exempel: om ett avbrott uppstår. Om du vill göra detta, i Azure-portalen, väljer du den virtuella datorn > **omsynkronisera**.

    ![Manuell omsynkronisering](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Försök process

Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Återförsök klassificeras enligt beskrivningen i tabellen.

**Kategori** | **Detaljer**
--- | ---
**Oåterkalleliga fel** | Inga nya försök görs. Status för den virtuella datorn är **kritisk**, och administratören måste ingripa.<br/><br/> Exempel på dessa fel är en bruten VHD-kedja, ogiltigt tillstånd för replikerade virtuella datorn, nätverksfel för autentisering, auktoriseringsfel och virtuell dator hittades inte-fel (för fristående Hyper-V-servrar.
**Återkalleliga fel** | Återförsök sker varje replikeringsintervall, med ett exponentiellt undantagsläge som ökar återförsöksintervallet från det första försökets start med 1, 2, 4, 8 och 10 minuter. Om felet kvarstår försöker du var 30: e minut. Exempel på detta är nätverksfel, lite diskutrymme och lite minne.



## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad redundansväxling från den lokala Hyper-V-datorer till Azure. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. Kör en oplanerad redundansväxling om den primära platsen är inte tillgänglig.
2. Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
3. Du kör en redundansväxling. När den första fasen av redundans är klar bör du kunna se skapade virtuella datorer i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
4. Du etablerar därefter redundansen för att få åtkomst till arbetsbelastningen från den Virtuella Azure-replikdatorn.

När din lokala infrastruktur är igång igen kan du inte återställa. Återställning efter fel sker i tre steg:

1. Starta en planerad redundansväxling från Azure till lokal plats:
    - **Minimera stilleståndstiden**: Om du använder det här alternativet Site Recovery synkroniserar data före redundans. Den söker efter ändrade datablock och hämtar dem till den lokala platsen, när behåller virtuell Azure-dator som kör, minimerar avbrottstid. När du anger manuellt när redundansen ska utföra, Azure-Virtuella datorn stängs av och alla slutliga deltaändringar kopieras redundansen börjar.
    - **Fullständig nedladdning**: med det här alternativet synkroniseras data under en redundansväxling. Det här alternativet laddar ned hela disken. Det går snabbare eftersom ingen kontrollsummor beräknas, men det finns fler driftstopp. Använd det här alternativet om du har kört repliken virtuella Azure-datorer under en viss tid, eller om den lokala virtuella datorn har tagits bort.
    - **Skapa en virtuell dator**: du kan välja för att växla tillbaka till samma virtuella dator eller till en annan virtuell dator. Du kan ange att Site Recovery ska skapa den virtuella datorn om den inte redan finns.

2. När den första synkroniseringen är klar kan välja du för att slutföra redundansen. När den är klar kan du logga in på den lokala virtuella datorn för att kontrollera att allt fungerar som förväntat. Du kan se att den virtuella Azure-datorer har stoppats i Azure-portalen.
3.  Sedan kan etablera du redundansen för att avsluta och få åtkomst till arbetsbelastningen från den lokala virtuella datorn igen.
4. När arbetsbelastningar har återställts, aktiverar du omvänd replikering så att replikera lokala virtuella datorer till Azure igen.



## <a name="next-steps"></a>Nästa steg


Följ [den här självstudien](tutorial-prepare-azure.md) att komma igång med Hyper-V till Azure-replikering.


