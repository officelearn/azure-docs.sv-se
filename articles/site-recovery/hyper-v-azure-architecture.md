---
title: Hyper-V i Azure replikering arkitekturen i Azure Site Recovery | Microsoft Docs
description: "Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att replikera lokala virtuella datorer med Hyper-V (utan VMM) till Azure med tjänsten Azure Site Recovery."
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: dd3dcf325ed5a628c98ac63683440e1796aa8c3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Hyper-V kan Azure replikeringsarkitektur


Den här artikeln beskriver arkitektur och processer som används när du replikera växla över och återställa Hyper-V virtuella datorer (VM) mellan lokala Hyper-V-värdar och Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

Hyper-V-värdar kan du kan också hanteras i System Center Virtual Machine Manager (VMM) privata moln.



## <a name="architectural-components---hyper-v-without-vmm"></a>Strukturella komponenter - Hyper-V utan VMM

Följande tabell och bild ger en övergripande bild av de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar som inte hanteras av VMM.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration, Azure storage-konto och Azure-nätverk. | Replikerade data från lokala Virtuella arbetsbelastningar lagras i lagringskontot. Virtuella Azure-datorer skapas med den replikerade arbetsbelastningsdata när det uppstår redundans från din lokala plats.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Hyper-V** | Under distributionen av Site Recovery kan du samla in Hyper-V-värdar och kluster i Hyper-V-platser. Du installerar Azure Site Recovery Provider och Recovery Services-agenten på varje Hyper-V-dator. | Providern samordnar replikeringen med Site Recovery-tjänsten via Internet. Recovery Services-agenten hanterar datareplikeringen.<br/><br/> Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.
**Virtuella Hyper-V-datorer** | En eller flera virtuella datorer som körs på Hyper-V. | Inget behöver installeras på virtuella datorer.


**Hyper-V för Azure-arkitektur (utan VMM)**

![Arkitektur](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Strukturella komponenter - Hyper-V med VMM

Följande tabell och bild ger en övergripande bild av de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar hanteras i VMM-moln.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration, Azure storage-konto och Azure-nätverk. | Replikerade data från lokala Virtuella arbetsbelastningar lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade data när det uppstår redundans från din lokala plats.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**VMM-server** | VMM-servern har ett eller flera moln som innehåller Hyper-V-värdar. | Du installerar Site Recovery-providern på VMM-servern att samordna replikeringen med Site Recovery och registrera servern i Recovery Services-valvet.
**Hyper-V-värd** | En eller flera Hyper-V-värdar eller Hyper-V-kluster som hanteras av VMM. |  Du installerar Recovery Services-agenten på varje värd eller klustermedlem.
**Virtuella Hyper-V-datorer** | En eller flera virtuella datorer som körs på en Hyper-V-värdserver. | Du behöver inte installera något på de virtuella datorerna.
**Nätverk** | Logiska och virtuella datornätverk som konfigurerats på VMM-servern. Det Virtuella datornätverket ska kopplas till ett logiskt nätverk som är kopplad till molnet. | Virtuella datornätverk mappas till virtuella Azure-nätverk. När virtuella Azure-datorer skapas efter växling vid fel, läggs de till Azure-nätverk som är mappad till det Virtuella datornätverket.

**Hyper-V för Azure-arkitektur (med VMM)**

![Komponenter](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replikeringsprocessen

![Hyper-V-replikering Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikering och återställningsprocesser**


### <a name="enable-protection"></a>Aktivera skydd

1. När du har aktiverat skydd för en virtuell Hyper-V-dator i Azure Portal eller lokalt startar **Aktivera skydd**.
2. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering med de inställningar som du har konfigurerat.
3. Jobbet startar den initiala replikeringen genom att aktivera metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig VM-replikering och skicka de virtuella datorernas diskar till Azure.
4. Du kan övervaka jobbet på fliken **Jobs** (Jobb).      ![Lista över jobb](media/hyper-v-azure-architecture/image1.png) ![Detaljnivå för Aktivera skydd](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Inledande datareplikering

1. När den inledande replikeringen utlöses en [Hyper-V VM-ögonblicksbild](https://technet.microsoft.com/library/dd560637.aspx) ögonblicksbilden tas.
2. Virtuella hårddiskar på den virtuella datorn är replikerade ett i taget tills alla har kopierats till Azure. Detta kan ta en stund, beroende på VM-storlek och nätverkets bandbredd. [Lär dig hur](https://support.microsoft.com/kb/3056159) att öka nätverkets bandbredd.
3. Om det inträffar under den inledande replikeringen pågår, spårar ändringar i Hyper-V-replikering replikering spåraren som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad hrl-fil som skickas till den sekundära lagringsplatsen. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


### <a name="finalize-protection-process"></a>Slutför skydd process

1. När den inledande replikeringen är klar i **Slutför skydd på den virtuella datorn** jobbet körs. Konfigurerar den nätverk och andra inställningar för efter replikeringen så att den virtuella datorn är skyddad.
2. I det här skedet kan du kontrollera VM-inställningar för att se till att den är redo för redundans. Du kan köra en katastrof återställningsgranskning (testa redundans) för den virtuella datorn att kontrollera att den inte över som förväntat. 


## <a name="delta-replication"></a>Deltareplikering

1. Efter den första replikeringen börjar deltareplikering i enlighet med replikeringsprincipen.
2. Hyper-V-replikering replikering spåraren spårar ändringar i en virtuell hårddisk som hrl-filerna. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
3. Loggen har skickats till kundens lagringskonto. När en logg är på väg till Azure kan spåras ändringar i den primära disken i en loggfil i samma mapp.
4. Du kan övervaka den virtuella datorn i Azure portal under inledande och delta.

### <a name="resynchronization-process"></a>Omsynkroniseringen process

1. Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så markeras en virtuell dator för omsynkronisering.
    - Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering.
    -  Som standard schemaläggs omsynkroniseringen för automatisk körning utanför kontorstid.
1.  Omsynkroniseringen skickar deltadata.
    - Det minimerar mängden data som skickas av beräkna kontrollsummor för käll- och virtuella datorer.
    - Den använder en fast block segment algoritm där filer för källa och mål är indelade i fast segment.
    - Kontrollsummor för varje segment genereras. Dessa jämförs för att avgöra vilka block från källan måste tillämpas på målet.
2. När omsynkroniseringen är klar ska den normala deltareplikeringen återupptas.
3. Om du inte vill vänta tills omsynkroniseringen är standard utanför timmar kan du synkronisera om en virtuell dator manuellt. Om exempelvis ett avbrott inträffar. Om du vill göra detta, i Azure portal, väljer du den virtuella datorn > **synkronisera om**.

    ![Manuell omsynkronisering](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Gör processen

Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Försök klassificeras enligt beskrivningen i tabellen.

**Kategori** | **Detaljer**
--- | ---
**Oåterkalleliga fel** | Inga nya försök görs. Status för den virtuella datorn är **kritisk**, och administratören måste ingripa.<br/><br/> Exempel på dessa fel är en bruten VHD-kedja, ett ogiltigt tillstånd för replikerade virtuella datorn, fel för nätverk-autentisering, auktorisering, och Virtuella hittades inte-fel (för fristående Hyper-V-servrar.
**Återkalleliga fel** | Återförsök sker varje replikeringsintervall, med ett exponentiellt undantagsläge som ökar återförsöksintervallet från det första försökets start med 1, 2, 4, 8 och 10 minuter. Om felet kvarstår försöker du var 30: e minut. Exempel på dessa är nätverksfel och låg diskfel lite minne.



## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad växling från lokala Hyper-V virtuella datorer till Azure. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. Kör en oplanerad redundansväxling om den primära platsen är inte tillgänglig.
2. Du kan växla över en enskild dator eller skapa återställningsplaner för att samordna växling vid fel på flera datorer.
3. Du kör en redundansväxling. När den första fasen av växling vid fel är klar bör du kunna se skapade virtuella datorer i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
4. Du sedan genomföra redundans för att starta åt arbetsbelastningen från repliken Azure VM.

När din lokala infrastruktur är igång kan växla du tillbaka. Återställning sker i tre steg:

1. Startar en planerad redundans från Azure till den lokala platsen:
    - **Minimera driftavbrott**: Om du använder det här alternativet Site Recovery synkroniserar data före redundans. Den söker efter ändrade datablock och hämtar dem till den lokala platsen när behåller virtuella Azure-datorn körs, minimerar avbrottstid. När du manuellt anger att redundans ska slutföra Azure VM är avstängd, kopieras alla slutliga deltaändringar och växling vid fel startar.
    - **Hämtar hela**: med det här alternativet synkroniseras data under växling vid fel. Det här alternativet laddar ned hela disken. Det går snabbare eftersom ingen kontrollsummor beräknas, men det finns flera avbrottstid. Använd det här alternativet om du har kört repliken Azure virtuella datorer under en viss tid, eller om den lokala virtuella datorn har tagits bort.
    - **Skapa virtuell dator**: du kan välja för att växla tillbaka till samma virtuella dator eller en annan virtuell dator. Du kan ange att Site Recovery ska skapa den virtuella datorn om den inte redan finns.

2. När den första synkroniseringen är klar kan välja du för att slutföra redundansen. När den är klar kan du logga in på den lokala virtuella datorn att kontrollera att allt fungerar som förväntat. Du kan se att den virtuella Azure-datorer har stoppats på Azure-portalen.
3.  Sedan kan genomföra du redundansen för att avsluta och starta åtkomst till arbetsbelastningen från den lokala virtuella datorn igen.
4. När arbetsbelastningar misslyckas igen kan aktivera du omvänd replikering så att replikera lokala virtuella datorer till Azure igen.



## <a name="next-steps"></a>Nästa steg


Följ [självstudierna](tutorial-prepare-azure.md) att komma igång med Hyper-V till Azure-replikering.


