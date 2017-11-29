---
title: "Återställning efter fel i Azure Site Recovery för Hyper-v-datorer | Microsoft Docs"
description: "Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Mer information om återställning från Azure till lokala datacenter."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: fafaf3f55f07741d438a06e58713d57d465b1137
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="failback-in-site-recovery-for-hyper-v-virtual-machines"></a>Återställning efter fel i Site Recovery för Hyper-V virtuella datorer

Den här artikeln beskriver hur du återställning av virtuella datorer som skyddas av Site Recovery.

## <a name="prerequisites"></a>Krav
1. Kontrollera att den primära VMM servern/Hyper-V-servern är ansluten.
2. Du bör ha utfört **genomför** på den virtuella datorn.

## <a name="why-is-there-no-button-called-failback"></a>Varför finns det ingen knapp som kallas återställning efter fel?
Det finns ingen uttrycklig gest kallas återställning efter fel på portalen. Återställning är ett steg där du kommer tillbaka till den primära platsen. Per definition är växling vid fel när du växling vid fel plats de virtuella datorerna från primary(on-premises) till återställning (Azure) och återställning när du redundans de virtuella datorerna från recovery tillbaka till primär.

När du påbörja en växling informerar bladet om riktningen för jobbet. Om riktningen är från Azure till lokala, är det en återställning efter fel.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Varför är det bara en planerad redundans gest för återställning efter fel?
Azure är en miljö med hög tillgänglighet och virtuella datorer är alltid tillgängliga. Återställning är en planerad aktivitet där du vill ta ett litet driftstopp så att arbetsbelastningarna kan starta körs lokalt igen. Detta förväntar sig inga data går förlorade. Därför bara en planerad redundans gest är tillgänglig, som ska stänga av virtuella datorer i Azure, ladda ned de senaste ändringarna och se till att det finns inga data går förlorade.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Behöver jag en processerver i Azure för återställning till Hyper-v?
Nej, krävs en processerver bara när du skyddar virtuella VMware-datorer. Inga ytterligare komponenter krävs för att distribueras när skydda/återställning efter fel för Hyper-v-datorer.

## <a name="initiate-failback"></a>Initiera återställning efter fel
Replikerade virtuella datorer som inte skyddas av Site Recovery efter växling från primär till sekundär plats, och den sekundära platsen nu fungerar som den aktiva platsen. Följ dessa procedurer för att växla tillbaka till den ursprungliga primära platsen. Den här proceduren beskriver hur du kör en planerad redundans för en återställningsplan. Du kan också köra redundans för en enskild virtuell dator den **virtuella datorer** fliken.

1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **redundans** > **planerad redundans**.
2. På den ** bekräfta planerad redundans ** väljer käll- och målplatserna. Observera riktning för växling vid fel. Om redundansväxling från primär fungerade som förväntat och alla virtuella datorer finns på sekundär plats som detta är endast i informationssyfte.
3. Om du växlar tillbaka från Azure väljer inställningar i **datasynkronisering**:

   * **Synkronisera data innan redundans (synkronisera deltaändringar endast)**– det här alternativet minimerar avbrottstid för virtuella datorer som det synkroniserar utan att de stängs av. Det gör du följande:
     * Fas 1: Tar en ögonblicksbild av den virtuella datorn i Azure och kopieras till den lokala Hyper-V-värden. Datorn fortsätter att köra i Azure.
     * Fas 2: Stängs den virtuella datorn i Azure så att inga nya ändringar sker det. Den slutgiltiga uppsättningen delta ändringar överförs till den lokala servern och den lokala virtuella datorn startas.

    - **Synkronisera data under växling vid fel endast (fullständig hämtning)**– Använd det här alternativet om du har kört i Azure under lång tid. Det här alternativet är snabbare eftersom vi räknar med att de flesta av disken har ändrats och vi vill inte ägna tid i beräkningen av kontrollsumma. Den genomför en hämtning av disken. Det är också användbart när den lokala virtuella datorn har tagits bort.

    >[!NOTE]
    >Vi rekommenderar att du använder det här alternativet om du har kört Azure på ett tag (en månad eller längre) eller den lokala virtuella datorn har tagits bort. Det här alternativet utföra inte några beräkningar av kontrollsumma.


4. Om datakryptering är aktiverat för molnet i **krypteringsnyckeln** Välj det certifikat som utfärdades när du har aktiverat datakryptering under providerinstallationen på VMM-servern.
5. Påbörja redundans. Du kan följa förloppet för växling vid fel på den **jobb** fliken.
6. Om du har valt alternativet att synkronisera data innan växling vid fel, när den inledande datasynkroniseringen är klar och du är redo att stänga av virtuella datorer i Azure, klickar du på **jobb** planerad redundans jobbnamn **slutföra växling vid fel**. Detta stängs av Azure-dator, överförs de senaste ändringarna till den lokala virtuella datorn och startar den virtuella dator lokalt.
7. Du kan nu logga in på den virtuella datorn och verifiera den är tillgänglig som förväntat.
8. Den virtuella datorn är i en bekräftelse av väntande tillstånd. Klicka på **Commit** att genomföra redundans.
9. Nu för att slutföra återställning efter fel klicka **omvänd replikera** att börja skydda den virtuella datorn i den primära platsen.

## <a name="failback-to-an-alternate-location"></a>Återställning till en alternativ plats
Om du har distribuerat skydd mellan en [Hyper-V-platsen och Azure](site-recovery-hyper-v-site-to-azure.md) du har möjlighet att återställning efter fel från Azure till en alternativ lokal plats. Detta är användbart om du behöver konfigurera ny lokal maskinvara. Här är hur du gör.

1. Om du ställer in ny maskinvara installera Windows Server 2012 R2 och Hyper-V-rollen på servern.
2. Skapa en virtuell nätverksswitch med samma namn som du hade på den ursprungliga servern.
3. Välj **skyddade objekt** -> **Skyddsgrupp**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> du vill växla tillbaka och välja **planerad redundans**.
4. I **bekräfta planerad redundans** Välj **skapa lokala virtuella datorn om det inte finns**.
5. Värddatornamn ** markerar du den nya Hyper-V-värdservern som du vill placera den virtuella datorn.
6. I datasynkronisering, rekommenderar vi du väljer alternativet **synkronisera data innan redundansväxlingen**. Detta minimerar avbrottstid för virtuella datorer som det synkroniserar utan att de stängs av. Det gör följande:

   * Fas 1: Tar en ögonblicksbild av den virtuella datorn i Azure och kopieras till den lokala Hyper-V-värden. Datorn fortsätter att köra i Azure.
   * Fas 2: Stängs den virtuella datorn i Azure så att inga nya ändringar sker det. Den slutgiltiga uppsättningen ändringar överförs till den lokala servern och den lokala virtuella datorn har startats.
7. Klicka på bockmarkeringen för att starta redundansväxlingen (återställning).
8. När den första synkroniseringen är klar och du är redo att stänga av den virtuella datorn i Azure, klickar du på **jobb** > <planned failover job> > **slutföra växling vid fel**. Detta stängs av Azure-dator, överförs de senaste ändringarna till den lokala virtuella datorn och startar den.
9. Du kan logga in på den lokala virtuella datorn att kontrollera att allt fungerar som förväntat. Klicka på **genomför** att slutföra växling vid fel.
10. Klicka på **omvänd replikera** att börja skydda lokala virtuella datorn.

    > [!NOTE]
    > Om du avbryter ett återställningsjobb när den är i datasynkronisering steg, blir den lokala virtuella datorn i ett skadat tillstånd. Detta beror på att datasynkronisering kopierar den senaste informationen från Azure VM-diskarna till lokala datadiskar och tills synkroniseringen är klar diskens data kanske inte i ett konsekvent tillstånd. Om den virtuella datorn på lokal startas när synkronisering har avbrutits, kan den inte startas. Åtgärden redundans för att slutföra den datasynkronisering.

## <a name="time-taken-to-failback"></a>Åtgången tid för att återställning efter fel
Den tid det tar att slutföra datasynkroniseringen och starta den virtuella datorn är beroende av flera faktorer. Om du vill ge en inblick i den tid det tar förklarar vi vad som händer under synkronisering.

Datasynkronisering tar en ögonblicksbild av den virtuella datorns diskar och börjar kontrollera block för block och beräknar dess kontrollsumma. Det här beräknade kontrollsumma skickas till lokala ska jämföras med den lokala kontrollsumman för samma block. Om kontrollsummor matchar överförs inte varje datablock. Om den inte matchar överförs varje datablock till lokalt. Den här överföringstiden är beroende av den tillgängliga bandbredden. Kontrollsumman hastighet är ett par GB per minut. 

Du kan konfigurera MARS-agenten för att använda flera trådar för att parallelize hämtningen för att påskynda hämtning av data. Referera till den [dokumentet här](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om hur du ändrar download trådarna i agenten.


## <a name="next-steps"></a>Nästa steg

När du har slutfört ett återställningsjobb **genomför** den virtuella datorn. Commit tar bort den virtuella Azure-datorn och dess diskar och förbereder den virtuella datorn som ska skyddas igen.

Efter **genomför**, kan du initiera den *omvänd replikera*. Detta startar skydda den virtuella datorn från lokala tillbaka till Azure. Detta replikeras endast ändringar sedan den virtuella datorn har inaktiverats i Azure och därför skickar databasfildifferentierade ändringar.
