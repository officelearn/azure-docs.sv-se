---
title: Kör en återställning efter fel under haveri beredskap för virtuella Hyper-v-datorer från Azure till lokal | Microsoft Docs
description: Lär dig att återställa virtuella Hyper-V-datorer till en lokal plats under haveri beredskap till Azure med tjänsten Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 07ecc8547ab155600bccfd1ad8f1ecbb58a18fa3
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931850"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Köra en återställning efter fel för virtuella Hyper-V-datorer

Den här artikeln beskriver hur du återställer virtuella Hyper-V-datorer som skyddas av Site Recovery.

## <a name="prerequisites"></a>Förutsättningar

- Se till att du har läst informationen om de [olika typerna av återställning efter fel](concepts-types-of-failback.md) och motsvarande varningar.
- Se till att VMM-servern för den primära platsen eller Hyper-V-värdservern är ansluten till Azure.
- Du bör ha utfört **incheckning** på den virtuella datorn.
- Se till att du använder ett lagrings konto för replikering och inte hanterade diskar. Återställning efter fel för virtuella Hyper-V-datorer som replikeras med hjälp av hanterings diskar stöds inte.

## <a name="perform-failback"></a>Utför återställning efter fel
Efter redundansväxlingen från den primära till den sekundära platsen, skyddas inte replikerade virtuella datorer av Site Recovery och den sekundära platsen fungerar nu som den aktiva platsen. Om du vill återställa virtuella datorer i en återställnings plan kör du en planerad redundansväxling från den sekundära platsen till den primära, enligt följande. 
1. Välj **återställnings planer** > *recoveryplan_name*. Klicka på**planerad redundansväxling**. > 
2. På sidan **Bekräfta Planerad redundans** väljer du käll-och mål platserna. Observera växlings riktningen. Om redundansväxlingen från det primära fungerade som förväntat och alla virtuella datorer finns på den sekundära platsen är detta endast för information.
3. Om du växlar tillbaka från Azure väljer du inställningar i **datasynkronisering**:
    - **Synkronisera data före redundans (synkronisera ändringar endast)** – med det här alternativet minimeras stillestånds tiden för virtuella datorer när den synkroniseras utan att stängas av. Följande steg visas:
        - Fas 1: Tar ögonblicks bilder av den virtuella datorn i Azure och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
        - Fas 2: Stänger av den virtuella datorn i Azure så att inga nya ändringar sker där. Den slutliga uppsättningen delta ändringar överförs till den lokala servern och den lokala virtuella datorn startas.

    - **Synkronisera endast data under redundans (fullständig hämtning)** – det här alternativet är snabbare.
        - Det här alternativet är snabbare eftersom vi förväntar dig att det mesta av disken har ändrats och vi inte vill ägna tid åt att beräkna beräkning av summor. Den utför en nedladdning av disken. Det är också användbart när den virtuella lokal-datorn har tagits bort.
        - Vi rekommenderar att du använder det här alternativet om du har kört Azure en stund (en månad eller mer) eller om den virtuella lokal-datorn har tagits bort. Det här alternativet utför inte några beräkningar av kontroll summa.


4. Om data kryptering är aktiverat för molnet väljer du det certifikat som utfärdades när du aktiverade data kryptering under installationen av providern på VMM-servern i **krypterings nyckel** .
5. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
6. Om du har valt alternativet för att synkronisera data före redundansväxlingen när den inledande datasynkroniseringen har slutförts och du är redo att stänga av de virtuella datorerna i Azure klickar du på **jobb** > jobb namn > **Slutför redundans**. Detta stänger av Azure-datorn, överför de senaste ändringarna till den lokala virtuella datorn och startar den virtuella datorn lokalt.
7. Nu kan du logga in på den virtuella datorn för att kontrol lera att den är tillgänglig som förväntat.
8. Den virtuella datorn är i ett väntande tillstånd. Klicka på **genomför** för att genomföra redundansväxlingen.
9. Slutför återställning efter fel genom att klicka på **omvänd replikering** för att börja skydda den virtuella datorn på den primära platsen.


Följ dessa procedurer för att växla tillbaka till den ursprungliga primära platsen. Den här proceduren beskriver hur du kör en planerad redundansväxling för en återställnings plan. Du kan också köra redundansväxlingen för en enskild virtuell dator på fliken **Virtual Machines** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Återställning efter fel till en alternativ plats i Hyper-V-miljön
Om du har distribuerat skydd mellan en [Hyper-V-plats och Azure](site-recovery-hyper-v-site-to-azure.md) måste du kunna återställa efter fel från Azure till en annan lokal plats. Detta är användbart om du behöver konfigurera en ny lokal maskin vara. Så här gör du det.

1. Om du installerar ny maskin vara installerar du Windows Server 2012 R2 och Hyper-V-rollen på-servern.
2. Skapa en virtuell nätverks växel med samma namn som du hade på den ursprungliga servern.
3. Välj**skydds grupp** ->  \< -> förskyddadeobjekt ProtectionGroupName >-> VirtualMachineName > du vill återställa igen och välj **Planerad redundans.** \<
4. I **Bekräfta Planerad redundans** väljer **du skapa en lokal virtuell dator om den inte finns**.
5. I värdnamn, * * väljer du den nya Hyper-V-värdservern där du vill placera den virtuella datorn.
6. I datasynkronisering rekommenderar vi att du väljer alternativet för att synkronisera data före redundansväxlingen. Detta minimerar stillestånds tiden för virtuella datorer när den synkroniseras utan att stänga av dem. Det gör följande:

    - Fas 1: Tar ögonblicks bilder av den virtuella datorn i Azure och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
    - Fas 2: Stänger av den virtuella datorn i Azure så att inga nya ändringar sker där. Den slutliga uppsättningen ändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    
7. Klicka på bock markeringen för att påbörja redundansväxlingen (failback).
8. När den första synkroniseringen är klar och du är redo att stänga av den virtuella datorn i Azure klickar du på **jobb** > \<planerad redundansväxling > > **Slutför redundans**. Detta stänger av Azure-datorn, överför de senaste ändringarna till den lokala virtuella datorn och startar den.
9. Du kan logga in på den lokala virtuella datorn för att kontrol lera att allt fungerar som förväntat. Klicka sedan på **genomför** för att slutföra redundansväxlingen. Commit tar bort den virtuella Azure-datorn och dess diskar och förbereder den virtuella datorn så att den skyddas igen.
10. Klicka på **omvänd replikering** för att börja skydda den lokala virtuella datorn.

    > [!NOTE]
    > Om du avbryter återställnings jobbet när det är i steget datasynkronisering, är den lokala virtuella datorn i ett skadat tillstånd. Detta beror på att datasynkroniseringen kopierar senaste data från virtuella Azure-diskar till lokal-datadiskarna, och tills synkroniseringen har slutförts kanske disk data inte är i ett konsekvent tillstånd. Om den virtuella datorn lokal startas efter att datasynkroniseringen har avbrutits, kan det hända att den inte startar. Utlös redundans för att slutföra datasynkroniseringen.


## <a name="why-is-there-no-button-called-failback"></a>Varför kallas ingen knapp återställning vid fel?
Det finns ingen explicit gest i portalen som kallas failback. Återställning efter fel är ett steg där du kommer tillbaka till den primära platsen. Efter definition är failback när du redundansväxlas de virtuella datorerna från återställning tillbaka till primär.

När du initierar en redundansväxling informerar bladet om i vilken riktning de virtuella datorerna ska flyttas, om riktningen är från Azure till lokal, är det en återställning efter fel.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Varför finns det bara en gest för planerad redundans för återställning efter fel?
Azure är en miljö med hög tillgänglighet och dina virtuella datorer är alltid tillgängliga. Failback är en planerad aktivitet där du bestämmer dig för att ta en liten nedtid så att arbets belastningarna kan börja köras lokalt. Detta förväntar sig ingen data förlust. Därför är bara en gest för planerad redundans tillgänglig, som stänger av de virtuella datorerna i Azure, laddar ned de senaste ändringarna och se till att det inte finns någon data förlust.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Behöver jag en processerver i Azure för återställning efter fel till Hyper-v?
Nej, en processerver krävs bara när du skyddar virtuella VMware-datorer. Inga ytterligare komponenter krävs för distribution vid skydd/återställning av virtuella Hyper-v-datorer.


## <a name="time-taken-to-failback"></a>Tids åtgång för återställning efter fel
Den tid det tar att slutföra datasynkroniseringen och starta den virtuella datorn beror på olika faktorer. För att ge en insikt om hur lång tid det tar, förklarar vi vad som händer under datasynkroniseringen.

Datasynkroniseringen tar en ögonblicks bild av den virtuella datorns diskar och startar kontrollen blockeras av block och beräknar dess kontroll summa. Den här beräknade kontroll summan skickas till den lokala platsen för att jämföra med den lokala kontroll summan för samma block. Om kontroll summorna matchar, överförs inte data blocket. Om den inte matchar överförs data blocket till lokalt. Den här överförings tiden beror på tillgänglig bandbredd. Kontroll summans hastighet är några GB per minut. 

För att påskynda hämtningen av data kan du konfigurera MARS-agenten att använda fler trådar för att parallellisera nedladdningen. Läs [dokumentet här](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om hur du ändrar hämtnings trådarna i agenten.


## <a name="next-steps"></a>Nästa steg

När du har **bekräftat**kan du starta den *omvända replikeringen*. Detta börjar skydda den virtuella datorn från en lokal plats tillbaka till Azure. Detta kommer endast att replikera ändringarna eftersom den virtuella datorn har inaktiverats i Azure och skickar därmed endast differentiella ändringar.
