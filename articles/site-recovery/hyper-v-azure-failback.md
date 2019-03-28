---
title: Köra en återställning efter fel under katastrofåterställning av Hyper-v-datorer från Azure till lokala | Microsoft Docs
description: Lär dig hur du växlar tillbaka Hyper-V-datorer till en lokal plats under haveriberedskap till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 4030b1905f8d5b50ef6be3ffa61eda74d8a27951
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541060"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Köra en återställning efter fel för Hyper-V-datorer

Den här artikeln beskriver hur du växlar tillbaka Hyper-V virtuella datorer som skyddas av Site Recovery.

## <a name="prerequisites"></a>Förutsättningar
1. Se till att du har läst informationen om den [olika typer av återställning efter fel](concepts-types-of-failback.md) och motsvarande varningar.
1. Kontrollera att den primära VMM-servern eller Hyper-V-värdservern är ansluten till Azure.
2. Du bör ha utfört **genomför** på den virtuella datorn.

## <a name="perform-failback"></a>Utför återställning efter fel
Den replikerade virtuella datorer som inte skyddas av Site Recovery efter en redundansväxling från primär till sekundär plats, och den sekundära platsen nu fungerar som den aktiva platsen. Inte säkerhetskopiera virtuella datorer i en återställningsplan som kör en planerad redundans från den sekundära platsen till primärt på följande sätt. 
1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **redundans** > **planerad redundans**.
2. På den **bekräfta planerad redundans** väljer käll- och målplatserna. Observera redundansriktningen. Om redundansväxling från den primära fungerade som förväntat och alla virtuella datorer finns i den sekundära platsen som detta är endast i informationssyfte.
3. Om du växlar tillbaka från Azure Välj inställningar i **datasynkronisering**:
    - **Synkronisera data före redundansväxling (synkronisera deltaändringar endast)**– det här alternativet minimerar avbrottstid för virtuella datorer eftersom den synkroniserar utan att de stängs av. Det gör du följande steg:
        - Fas 1: Hämtar ögonblicksbild av den virtuella datorn i Azure och kopieras till en lokal Hyper-V-värd. Datorn fortsätter att köra i Azure.
        - Fas 2: Stänger av den virtuella datorn i Azure så att inga nya ändringar sker det. Den slutgiltiga uppsättningen av deltaändringar överförs till den lokala servern och den lokala virtuella datorn startas.

    - **Synkronisera data under endast redundans (fullständig nedladdning)**– det här alternativet är snabbare.
        - Det här alternativet är snabbare eftersom vi förväntar oss att de flesta av disken har ändrats och vi vill inte använda kontrollsummeberäkningen. Den utför en nedladdning av disken. Det är också användbart när den lokala virtuella datorn har tagits bort.
        - Vi rekommenderar att du använder det här alternativet om du har kört Azure ett tag (en månad eller mer) eller en lokal virtuell dator har tagits bort. Det här alternativet utföra inte några beräkningar av kontrollsumma.


4. Om datakryptering är aktiverat för molnet, i **krypteringsnyckeln** välja det certifikat som utfärdades när du har aktiverat kryptering av data under providerinstallationen på VMM-servern.
5. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
6. Om du har valt alternativet att synkronisera data innan redundansen, när den inledande datasynkroniseringen är klar och du är redo att stänga av virtuella datorer i Azure, klickar du på **jobb** > jobbnamn >  **Slutför Redundansväxlingen**. Detta stänger av Azure-dator, överför de senaste ändringarna till den lokala virtuella datorn och startar de virtuella datorn lokalt.
7. Du kan logga in på den virtuella datorn för att verifiera att den är tillgänglig som förväntat.
8. Den virtuella datorn är i ett åtagande väntetillstånd. Klicka på **Commit** att genomföra redundans.
9. Slutför återställningen genom att klicka på **omvänd replikera** att börja skydda den virtuella datorn i den primära platsen.


Använd metoderna nedan för att växla över till den ursprungliga primära platsen. Den här proceduren beskriver hur du kör en planerad redundans för en återställningsplan. Du kan också köra redundans för en enskild virtuell dator den **virtuella datorer** fliken.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Växla tillbaka till en annan plats i Hyper-V-miljö
Om du har distribuerat skydd mellan en [Hyper-V-platsen och Azure](site-recovery-hyper-v-site-to-azure.md) du har möjlighet till återställning efter fel från Azure till en annan lokal plats. Detta är användbart om du vill ställa in nya lokal maskinvara. Här är hur du gör.

1. Om du konfigurerar ny maskinvara du installera Windows Server 2012 R2 och Hyper-V-rollen på servern.
2. Skapa en virtuell nätverksswitch med samma namn som fanns på den ursprungliga servern.
3. Välj **skyddade objekt** -> **Skyddsgrupp** -> \<ProtectionGroupName > -> \<VirtualMachineName > du vill växla tillbaka, Välj **planerad redundans**.
4. I **bekräfta planerad redundans** Välj **skapa lokala virtuella datorn om den inte finns**.
5. Värddatornamn ** väljer den nya Hyper-V-värdservern som du vill placera den virtuella datorn.
6. I datasynkronisering rekommenderar vi att du väljer alternativet att synkronisera data innan redundansväxlingen sker. Detta minimerar avbrottstid för virtuella datorer eftersom den synkroniserar utan att de stängs av. Det gör följande:

    - Fas 1: Hämtar ögonblicksbild av den virtuella datorn i Azure och kopieras till en lokal Hyper-V-värd. Datorn fortsätter att köra i Azure.
    - Fas 2: Stänger av den virtuella datorn i Azure så att inga nya ändringar sker det. Den slutgiltiga uppsättningen ändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    
7. Klicka på bockmarkeringen för att påbörja redundans (återställning).
8. När den inledande synkroniseringen är klar och du är redo att Stäng av den virtuella datorn i Azure, klickar du på **jobb** > \<planerad redundans jobbet >> **slutföra växling vid fel** . Detta stänger av Azure-dator, överför de senaste ändringarna till den lokala virtuella datorn och startar den.
9. Du kan logga in på den lokala virtuella datorn att kontrollera att allt fungerar som förväntat. Klicka sedan på **genomför** att slutföra redundansväxlingen. Commit tar bort Azure-dator och dess diskar och förbereder den virtuella datorn som ska skyddas igen.
10. Klicka på **omvänd replikera** att börja skydda den lokala virtuella datorn.

    > [!NOTE]
    > Om du avbryter ett återställningsjobb i datasynkronisering steg, blir den lokala virtuella datorn i ett skadat tillstånd. Detta beror datasynkronisering kopierar den senaste informationen från Virtuella Azure-diskar till en lokal datadiskar och tills synkroniseringen är klar diskens data kanske inte är i ett konsekvent tillstånd. Om den virtuella datorn lokalt startas när synkronisering har avbrutits, kan den inte startas. Utlös igen redundans för att slutföra den datasynkronisering.


## <a name="why-is-there-no-button-called-failback"></a>Varför finns det ingen knapp som kallas återställning efter fel?
Det finns ingen explicit gest som kallas återställning efter fel på portalen. Återställning efter fel är ett steg där du kommer tillbaka till den primära platsen. Per definition är återställning efter fel när redundans de virtuella datorerna från recovery tillbaka till primär.

När du initierar en växling vid fel, meddelar dig om den riktning som de virtuella datorerna är flyttas, om riktningen är från Azure till lokalt, är det en återställning efter fel i bladet.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Varför är det bara en planerad redundans gest för återställning efter fel?
Azure är en mycket tillgänglig miljö och dina virtuella datorer är alltid tillgängliga. Återställning efter fel är en planerad aktivitet där du vill ta en liten avbrott så att arbetsbelastningarna som kan starta som körs lokalt igen. Detta förväntar sig att inga data går förlorade. Därför bara en planerad redundans gest finns, som ska stänga av virtuella datorer i Azure, hämta de senaste ändringarna och se till att det finns inga data går förlorade.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Behöver jag en processerver i Azure för återställning efter fel till Hyper-v?
Nej, krävs en processerver bara när du skyddar virtuella VMware-datorer. Det krävs inga ytterligare komponenter som ska distribueras när du skyddar/återställning av Hyper-v-datorer.


## <a name="time-taken-to-failback"></a>Tid för återställning efter fel
Den tid det tar att slutföra datasynkroniseringen och starta den virtuella datorn är beroende av olika faktorer. Om du vill ge en överblick över den tid det tar förklarar vi vad som händer under synkronisering.

Synkronisering tar en ögonblicksbild av den virtuella datorns diskar och börjar kontrollera block för block och beräknar dess kontrollsumma. Det här beräknade kontrollsumma skickas till den lokala ska jämföras med den lokala kontrollsumman för samma block. Om kontrollsummor matchar, överförs inte datablocket. Om det inte matchar överförs varje datablock till den lokala. Den här överföringstiden beror på den tillgängliga bandbredden. Hastigheten på kontrollsumman är några få GB per minut. 

För att påskynda hämtningen av data, kan du konfigurera MARS-agenten för att använda fler trådar för att parallellisera nedladdningen. Referera till den [beskriva här](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om hur du ändrar download trådar i agenten.


## <a name="next-steps"></a>Nästa steg

Efter **genomför**, du kan starta den *omvänd replikera*. Detta startar skydda den virtuella datorn från en lokal plats till Azure. Detta kommer endast replikera ändringarna eftersom den virtuella datorn har inaktiverats i Azure och kan därför skickar databasfildifferentierade ändringar.
