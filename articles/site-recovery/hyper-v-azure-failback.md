---
title: Misslyckas med att återställa virtuella hyper-virtuella datorer från Azure med Azure Site Recovery
description: Så här växlar du tillbaka hyper-virtuella datorer till en lokal plats från Azure med Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281787"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Köra en återställning efter fel för virtuella Hyper-V-datorer

I den här artikeln beskrivs hur du reder tillbaka virtuella Azure-datorer som har skapats efter redundans av virtuella hyper-virtuella datorer från en lokal plats till Azure, med [Azure Site Recovery](site-recovery-overview.md).

- Du kan återställa Hyper-V-datorer från Azure genom att köra en planerad redundans från Azure till den lokala platsen. Om redundansriktningen är från Azure till lokalt betraktas det som en återställning efter fel.
- Eftersom Azure är en miljö med hög tillgänglig tillgång och virtuella datorer alltid är tillgängliga, är återställning efter fel från Azure en planerad aktivitet. Du kan planera för ett litet driftstopp så att arbetsbelastningar kan börja köras lokalt igen. 
- Planerad återställning av återställning efter fel inaktiverar de virtuella datorerna i Azure och hämtar de senaste ändringarna. Ingen dataförlust förväntas.

## <a name="before-you-start"></a>Innan du börjar

1. [Granska de typer av återställning av fel som](failover-failback-overview.md#hyper-v-reprotectionfailback) du kan använda – original återställning av plats och alternativ platsåterställning.
2. Kontrollera att virtuella Azure-datorer använder ett lagringskonto och inte hanterade diskar. Återställning av virtuella hyper-virtuella datorer som replikeras med hanterade diskar stöds inte.
3. Kontrollera att den lokala Hyper-V-värden (eller System Center VMM-servern om du använder med Site Recovery) körs och är ansluten till Azure. 
4. Kontrollera att redundans och genomförande är slutförd för de virtuella datorerna. Du behöver inte konfigurera några specifika site recovery-komponenter för återställning av hyper-virtuella datorer från Azure.
5. Den tid som krävs för att slutföra datasynkronisering och starta den lokala virtuella datorn beror på ett antal faktorer. För att påskynda datahämtningen kan du konfigurera Microsoft Recovery Services-agenten så att fler trådar kan parallellisera hämtningen. [Läs mer](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Växla tillbaka till den ursprungliga platsen

Om du vill återställa hyper-virtuella datorer i Azure till den ursprungliga lokala virtuella datorn kör du en planerad redundans från Azure till den lokala platsen enligt följande:

1. Välj den virtuella datorn i valvet > **replikerade objekt.** Högerklicka på den virtuella datorn > **planerad redundans**. Om du inte säkerhetskopierar en återställningsplan markerar du plannamnet och klickar på > **Redundanplanerad redundans**. **Failover**
2. I **Bekräfta planerad redundans**väljer du käll- och målplatser. Notera redundansriktningen. Om redundans från primärt fungerade som förväntat och alla virtuella datorer är på den sekundära platsen är detta endast för information.
3. Välj ett alternativ **i Datasynkronisering:**
    - **Synkronisera data före redundans (endast synkronisering av deltaändringar)**– Det här alternativet minimerar stilleståndstiden för virtuella datorer när de synkroniseras utan att stänga av dem.
        - **Fas 1**: Tar en ögonblicksbild av Azure VM och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
        - **Fas 2**: Stänger av den virtuella azure-datorn så att inga nya ändringar inträffar där. Den slutliga uppsättningen deltaändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    - **Synkronisera data endast under redundans (fullständig hämtning)**– Det här alternativet är snabbare eftersom vi antar att de flesta av disken har ändrats och inte vill spendera tid på att beräkna kontrollsummar. Det här alternativet utför inga kontrollsummenberäkningar.
        - Den utför en nedladdning av disken. 
        - Vi rekommenderar att du använder det här alternativet om du har kört Azure ett tag (en månad eller mer) eller om den lokala virtuella datorn tas bort.

4. Endast för VMM, om datakryptering är **Encryption Key**aktiverat för molnet, väljer du det certifikat som utfärdades när du aktiverade datakryptering under Provider-installationen på VMM-servern.
5. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
6. Om du valde alternativet att synkronisera data före redundansen klickar du på **Jobb** > jobbnamn > **Slutför redundans**när den första datasynkroniseringen är klar och du är redo att stänga av de virtuella datorerna i Azure. Det här gör följande:
    - Stänger av Azure-datorn.
    - Överför de senaste ändringarna till den lokala virtuella datorn.
    - Startar den lokala virtuella datorn.
7. Du kan nu logga in på den lokala VM-datorn för att kontrollera att den är tillgänglig som förväntat.
8. Den virtuella datorn är i ett väntande tillstånd. Klicka på **Commit** för att genomföra redundansen.
9. Om du vill slutföra återställningen klickar du på **Omvänd replikera** för att börja replikera den lokala virtuella datorn till Azure igen.



## <a name="fail-back-to-an-alternate-location"></a>Växla tillbaka till en alternativ plats 

Växla tillbaka till en alternativ plats enligt följande:

1. Om du konfigurerar ny maskinvara installerar du en [version av Windows som stöds](hyper-v-azure-support-matrix.md#replicated-vms)och Hyper-V-rollen på datorn.
2. Skapa en virtuell nätverksväxel med samma namn som du hade på den ursprungliga servern.
3. I **Skyddade objektSskydd** > **GruppskyddGroupName** > \<> -> \<VirtualMachineName> väljer du den virtuella dator som du vill återställa och väljer sedan Planerad **redundans**.
4. I **Bekräfta planerad redundans**väljer du **Skapa lokal virtuell dator om den inte finns**.
5. I **Värdnamn**väljer du den nya Hyper-V-värdservern där du vill placera den virtuella datorn.
6. I **Datasynkronisering**rekommenderar vi att du väljer alternativet att synkronisera data före redundansen. Detta minimerar driftstopp för virtuella datorer när den synkroniseras utan att stänga av dem. Det gör följande:
    - **Fas 1**: Tar ögonblicksbilder av Den virtuella Azure-datorn och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
    - **Fas 2**: Stänger av den virtuella azure-datorn så att inga nya ändringar inträffar där. Den slutliga uppsättningen ändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    
7. Klicka på bocken för att påbörja redundansen (redundans).
8. När den första synkroniseringen är klar och du är redo att stänga av den virtuella Azure-datorn klickar du på **Jobbplanerat** > \<redundansjobb> > **Slutför redundans**. Detta stänger av Azure-datorn, överför de senaste ändringarna till den lokala virtuella datorn och startar den.
9. Du kan logga in på den lokala virtuella datorn för att kontrollera att allt fungerar som förväntat.
10. Klicka på **Slutför** för att slutföra redundansen. Commit tar bort Den virtuella Azure-datorn och dess diskar och förbereder den lokala virtuella datorn för att skyddas igen.
10. Klicka på **Omvänd replikera** för att börja replikera den lokala virtuella datorn till Azure. Endast deltaändringarna sedan den virtuella datorn stängdes av i Azure replikeras.

    > [!NOTE]
    > Om du avbryter återställningsjobbet under datasynkronisering kommer den lokala virtuella datorn att vara i ett skadat tillstånd. Detta beror på att datasynkronisering kopierar de senaste data från Azure VM-diskar till lokala datadiskar, och tills synkroniseringen är klar, kan diskdata inte vara i ett konsekvent tillstånd. Om den lokala virtuella datorn startar efter att datasynkroniseringen har avbrutits kanske den inte startas. I det här fallet kör du redundansen igen för att slutföra datasynkroniseringen.


## <a name="next-steps"></a>Nästa steg
När den lokala virtuella datorn replikeras till Azure kan du [köra en annan redundans](site-recovery-failover.md) till Azure efter behov.
