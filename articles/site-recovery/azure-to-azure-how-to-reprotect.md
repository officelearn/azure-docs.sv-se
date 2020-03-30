---
title: Återrotera virtuella Azure-datorer till den primära regionen med Azure Site Recovery | Microsoft-dokument
description: Beskriver hur du roterar om virtuella Azure-datorer efter redundans, den sekundära till den primära regionen, med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 73747b8331054cdc3bfa1f4073ccf2cdb62ab326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283250"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Reprotect misslyckades över virtuella Azure-datorer till den primära regionen

När du [växlar över](site-recovery-failover.md) virtuella Azure-datorer från en region till en annan med hjälp av Azure [Site Recovery](site-recovery-overview.md)startar de virtuella datorerna upp i den sekundära regionen i **ett oskyddat** tillstånd. Om du vill återställa de virtuella datorerna till den primära regionen gör du följande:

1. Reprotect de virtuella datorerna i den sekundära regionen, så att de börjar replikera till den primära regionen.
1. När reprotection har slutförts och de virtuella datorerna replikeras kan du växla över från den sekundära till primära regionen.

## <a name="prerequisites"></a>Krav

- Fel vid felkoppling av den virtuella datorn från den primära till sekundära regionen måste bekräftas.
- Den primära målplatsen ska vara tillgänglig och du bör kunna komma åt eller skapa resurser i den regionen.

## <a name="reprotect-a-vm"></a>Reprotera en virtuell dator

1. Högerklicka på den misslyckade över den virtuella datorn **Re-Protect**i **Vault** > **Arkiv-replikerade objekt**och välj Skydda igen . Återskyddsriktningen ska visas från sekundär till primär.

   ![Reprotect](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Granska resursgrupps-, nätverks-, lagrings- och tillgänglighetsuppsättningarna. Klicka sedan på **OK**. Om det finns några resurser markerade som nya skapas de som en del av återskyddsprocessen.
1. Den reprotection jobb frön målet webbplats med de senaste uppgifterna. När jobbet är klart sker deltareplikering. Sedan kan du växla över tillbaka till den primära platsen. Du kan välja det lagringskonto eller det nätverk som du vill använda under återreptur, med hjälp av anpassa alternativet.

   ![Alternativet Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Anpassa inställningar för återrotect

Du kan anpassa följande egenskaper för måldatorn under återbeskyddning.

![Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap |Anteckningar  |
|---------|---------|
|Målgrupp för målresurs | Ändra målresursgruppen där den virtuella datorn skapas. Som en del av återskydd tas måldatorn bort. Du kan välja en ny resursgrupp där den virtuella datorn ska skapas efter redundans. |
|Rikta in dig på virtuellt nätverk | Målnätverket kan inte ändras under reprotect-jobbet. Om du vill ändra nätverket gör du om nätverksmappningen. |
|Mållagring (Sekundär virtuell dator använder inte hanterade diskar) | Du kan ändra lagringskontot som den virtuella datorn använder efter redundans. |
|Replikhanterade diskar (sekundär virtuell dator använder hanterade diskar) | Site Recovery skapar replikhanterade diskar i den primära regionen för att spegla den sekundära virtuella datorns hanterade diskar. |
|Cachelagring | Du kan ange ett cachelagringskonto som ska användas under replikeringen. Som standard skapas ett nytt cachelagringskonto om det inte finns. |
|Tillgänglighetsuppsättning | Om den virtuella datorn i den sekundära regionen är en del av en tillgänglighetsuppsättning kan du välja en tillgänglighetsuppsättning för måldatorn i den primära regionen. Som standard försöker Site Recovery hitta den befintliga tillgänglighetsuppsättningen i den primära regionen och använda den. Under anpassningen kan du ange en ny tillgänglighetsuppsättning. |

### <a name="what-happens-during-reprotection"></a>Vad händer under återbeskydd?

Som standard inträffar följande:

1. Ett cachelagringskonto skapas i den region där den misslyckade över den virtuella datorn körs.
1. Om mållagringskontot (det ursprungliga lagringskontot i den primära regionen) inte finns skapas ett nytt. Det tilldelade lagringskontonamnet är namnet på det lagringskonto som `asr`används av den sekundära virtuella datorn, suffixat med .
1. Om den virtuella datorn använder hanterade diskar skapas replikhanterade diskar i den primära regionen för att lagra data som replikeras från den sekundära virtuella datorns diskar.
1. Om måltillgänglighetsuppsättningen inte finns skapas en ny som en del av reprotect-jobbet om det behövs. Om du har anpassat återbeskyddningsinställningarna används den valda uppsättningen.

När du utlöser ett reprotect-jobb och måldatorn finns, inträffar följande:

1. Den virtuella målsidan är inaktiverad om den körs.
1. Om den virtuella datorn använder hanterade diskar skapas en `-ASRReplica` kopia av den ursprungliga disken med ett suffix. De ursprungliga diskarna tas bort. Kopiorna `-ASRReplica` används för replikering.
1. Om den virtuella datorn använder ohanterade diskar tas måldass datadiskarna bort och används för replikering. En kopia av OS-disken skapas och bifogas på den virtuella datorn. Den ursprungliga OS-disken är fristående och används för replikering.
1. Endast ändringar mellan källdisken och måldisken synkroniseras. Differentiella beräkningar beräknas genom att jämföra både diskarna och sedan överföras. Kontrollera nedan för att hitta den beräknade tiden för att slutföra reprotection.
1. När synkroniseringen är klar börjar deltareplikeringen och en återställningspunkt skapas i linje med replikeringsprincipen.

När du utlöser ett reprotect-jobb och måldatorn och diskarna inte finns, inträffar följande:

1. Om den virtuella datorn använder hanterade diskar `-ASRReplica` skapas replikdiskar med suffix. Kopiorna `-ASRReplica` används för replikering.
1. Om den virtuella datorn använder ohanterat diskar skapas replikdiskar i mållagringskontot.
1. Hela diskar kopieras från regionen failed over till det nya målområdet.
1. När synkroniseringen är klar börjar deltareplikeringen och en återställningspunkt skapas i linje med replikeringsprincipen.

#### <a name="estimated-time-to-do-the-reprotection"></a>Beräknad tid att göra reprotection

I de flesta fall replikerar Azure Site Recovery inte fullständiga data till källregionen.
Följande villkor avgör hur mycket data som replikeras:

1. Om käll-VM-data tas bort, är skadade eller otillgängliga av någon anledning, till exempel en resursgruppsändring/borttagning, sker en fullständig inledande replikering under återbeskyddning eftersom det inte finns några tillgängliga data i källområdet.
1. Om käll-VM-data är tillgängliga beräknas endast differentiella skillnader genom att jämföra båda diskarna och sedan överföras. Kontrollera tabellen nedan för att få den beräknade tiden.

|Exempel situation | Det tar tid att återrepesekera |
|---|---|
|Källregionen har 1 virtuell dator med 1 TB standarddisk.<br/>Endast 127 GB-data används och resten av disken är tom.<br/>Disktypen är standard med 60 MiB/S dataflöde.<br/>Inga data ändras efter redundans.| Ungefärlig tid: 45 minuter – 1,5 timmar.<br/>Under reprotection kommer Site Recovery att fylla i kontrollsumman för alla data som tar 127 GB/ 45 MB, cirka 45 minuter.<br/>Viss overheadtid krävs för att site recovery ska kunna skalas automatiskt, cirka 20-30 minuter.<br/>Inga utgående avgifter. |
|Källregionen har 1 virtuell dator med 1 TB standarddisk.<br/>Endast 127 GB-data används och resten av disken är tom.<br/>Disktypen är standard med 60 MiB/S dataflöde.<br/>45 GB data ändras efter redundans.| Ungefärlig tid: 1 timme – 2 timmar.<br/>Under reprotection kommer Site Recovery att fylla i kontrollsumman för alla data som tar 127 GB/ 45 MB, cirka 45 minuter.<br/>Överföringstid för att tillämpa ändringar på 45 GB som är 45 GB/ 45 MBps, cirka 17 minuter.<br/>Utgående avgifter skulle vara för 45 GB dataändringar, inte för kontrollsumman. |

När den virtuella datorn skyddas igen efter att ha misslyckats tillbaka till den primära regionen (dvs. om den virtuella datorn är åter skyddad från primär region till DR-region), tas måldatorn och associerade nätverkskort bort.

När den virtuella datorn skyddas från DR-regionen till den primära regionen tar vi inte bort den dåvarande primära virtuella datorn och tillhörande nätverkskort.

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har skyddats kan du initiera en redundans. Redundansen stänger av den virtuella datorn i den sekundära regionen och skapar och startar den virtuella datorn i den primära regionen, med korta driftstopp under den här processen. Vi rekommenderar att du väljer en lämplig tid för den här processen och att du kör en testundangång innan du initierar en fullständig redundans till den primära platsen. [Läs mer](site-recovery-failover.md) om redundans för Azure Site Recovery.
