---
title: Återaktivering av skydd redundansväxlade virtuella Azure-datorer till den primära Azure-regionen med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du skyddar virtuella Azure-datorer i en sekundär region efter redundansväxling från en primär region, med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: eabb7d194a3ef65282befab1ae59e85ba56f2f5b
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472163"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Återaktivering av skydd redundansväxlade virtuella Azure-datorer till den primära regionen


När du [redundansväxla](site-recovery-failover.md) Azure virtuella datorer från en region till en annan med [Azure Site Recovery](site-recovery-overview.md), virtuella datorer startas i den sekundära regionen oskyddad. Om de virtuella datorerna återställa till den primära regionen, måste du göra följande:

- Återaktivera skyddet av de virtuella datorerna i den sekundära regionen så att de börjar replikera till den primära regionen.
- När återaktiveringen av skyddet har slutförts och de virtuella datorerna replikeras, kan du växla över dem från sekundär till primär region.

## <a name="prerequisites"></a>Nödvändiga komponenter
1. VM-redundans från primärt till sekundära region måste bekräftas.
2. Den primära målplatsen ska vara tillgängliga och du ska kunna komma åt eller skapa resurser i den regionen.

## <a name="reprotect-a-vm"></a>Återaktivera skyddet av en virtuell dator

1. I **Vault** > **replikerade objekt**, högerklicka på den redundansväxlade virtuella datorn och välj **skydda igen**. Riktning för återaktiveringen av skyddet ska visa från sekundär till primär.

   ![Återaktivering av skydd](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Granska resource group, nätverk, lagring och tillgänglighet uppsättningar. Klicka sedan på **OK**. Om det finns några resurser som är markerade som ny, skapas de som en del av återaktiveringen av skyddet.
3. Återaktivering av skydd jobbet lägger till målplatsen den senaste informationen. Efter som har slutförts sker deltareplikering. Sedan kan du växla över till den primära platsen. Du kan välja storage-konto eller det nätverk som du vill använda under skydda igen med alternativet Anpassa.

   ![Anpassa alternativ](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Anpassa inställningar för återaktivering av skydd

Du kan anpassa följande egenskaper för mål VMe under återaktiveringen av skyddet.

![Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap  |Anteckningar  |
|---------|---------|
|Målresursgrupp     | Ändra målresursgruppen som den virtuella datorn har skapats. Den Virtuella måldatorn som en del av återaktiveringen av skyddet har tagits bort. Du kan välja en ny resursgrupp som du skapar den virtuella datorn efter redundans.        |
|Virtuellt målnätverk     | Målnätverket kan inte ändras under jobbet för återaktivering av skydd. Om du vill ändra nätverket, gör du om nätverksmappningen.         |
|Mål-Lagringskontot (sekundära virtuella datorn inte använder hanterade diskar)     | Du kan ändra det lagringskonto som den virtuella datorn använder efter en redundansväxling.         |
|Hanterade replikeringsdiskar (sekundära virtuella datorn använder hanterade diskar)    | Hanterade replikeringsdiskar skapar site Recovery i den primära regionen för spegling av hanterade diskar för den sekundära virtuella datorn.         |
|Cache Storage     | Du kan ange ett cachelagringskonto som ska användas vid replikering. Som standard är ett nytt cachelagringskonto skapas, om det inte finns.         |
|Tillgänglighetsuppsättning     |Om den virtuella datorn i den sekundära regionen är en del av en tillgänglighetsuppsättning, kan du välja en tillgänglighetsuppsättning för den Virtuella måldatorn i den primära regionen. Som standard Site Recovery försöker hitta befintliga tillgänglighetsuppsättningen i den primära regionen och använda den. Vid anpassning, kan du ange en ny tillgänglighetsuppsättning.         |


### <a name="what-happens-during-reprotection"></a>Vad händer under återaktiveringen av skyddet?

Som standard inträffar följande:

1. Ett cachelagringskonto skapas i regionen där den redundansväxlade virtuella datorn körs.
2. Om mål-lagringskontot (ursprungliga lagringskontot i den primära regionen) inte finns, skapas en ny. Tilldelade lagringskontonamn är namnet på det lagringskonto som används av den sekundära virtuella datorn, suffix med ”asr”.
3. Om den virtuella datorn använder hanterade diskar, hanterade diskar skapas i den primära regionen att lagra de data som replikeras från den sekundära Virtuella diskar.
4. Om målets tillgänglighetsuppsättning inte finns, skapas en ny som en del av jobbet återaktivering av skydd om det behövs. Om du har anpassade inställningar för återaktivering av skydd, används den valda uppsättningen.

När du utlöser ett jobb för återaktivering av skydd och de mål som den virtuella datorn finns, inträffar följande:

1. Målsidan virtuella datorn stängs av om den körs.
2. Om den virtuella datorn använder hanterade diskar, skapas en kopia av ursprungliga diskarna med ”-ASRReplica” suffix. De ursprungliga diskarna tas bort. Den ”-ASRReplica” kopior som används för replikering.
3. Om den virtuella datorn använder ohanterade diskar, är oberoende av den Virtuella måldatorns datadiskar och används för replikering. En kopia av OS-disken skapas och kopplas på den virtuella datorn. Den ursprungliga OS-disken är oberoende och används för replikering.
4. Endast ändringar mellan källdisken och måldisken är synkroniserade. Skillnader i beräknas genom att jämföra båda diskarna och sedan överföras. Att hitta kontrollen beräknad tid.
5. När synkroniseringen är klar börjar deltareplikeringen och skapar en återställningspunkt i enlighet med replikeringsprincipen.

När du utlöser ett jobb för återaktivering av skydd och den Virtuella måldatorn och diskar finns inte, inträffar följande:
1. Om den virtuella datorn använder hanterade diskar, replikeringsdiskar skapas med ”-ASRReplica” suffix. Den ”-ASRReplica” kopior som används för replikering.
2. Om den virtuella datorn använder ohanterade diskar kan skapas replikeringsdiskar i mållagringskontot.
3. Hela diskarna kopieras från den misslyckade över regionen till den nya målregionen.
4. När synkroniseringen är klar börjar deltareplikeringen och skapar en återställningspunkt i enlighet med replikeringsprincipen.

#### <a name="estimated-time--to-do-the-reprotection"></a>Beräknad tid för att göra återaktiveringen av skyddet 

I de flesta fall replikerar inte Azure Site Recovery fullständiga data till källregionen. Här följer du de villkor som avgör hur mycket data skulle replikeras:

1.  Om källan VM-data har tagits bort, skadad eller otillgänglig på grund av någon anledning som resursgruppen sker ändrar/tar bort sedan under återaktiveringen av skyddet fullständig IR eftersom det inte finns några data som är tillgängliga på käll-region som ska användas.
2.  Om källan data för virtuell dator är tillgänglig sedan endast skillnader beräknas genom att jämföra båda diskarna och sedan överföras. Kontrollera den tabellen att hämta den beräknade tiden för nedan 

|** Exempel situationen ** | ** Tid det tar att återaktivera skydd ** |
|--- | --- |
|Källregion har 1 virtuell dator med 1 TB standard Disk<br/>– Endast 127 GB data används och resten av disken är tom<br/>-Disktypen är standard med 60 MiB/S genomströmning<br/>– Ingen ändring av data efter en redundansväxling| Ungefärlig tid 45 minuter – 1,5 timmar<br/> -Under återaktiveringen av skyddet Site Recovery fylls kontrollsumman för hela data som tar 127 GB / 45 MB ~ 45 minuter<br/>-Administration tid som krävs för Site Recovery för att automatisk skalning som är 20 – 30 minuter<br/>– Inte för utgående trafik |
|Källregion har 1 virtuell dator med 1 TB standard Disk<br/>– Endast 127 GB data används och resten av disken är tom<br/>-Disktypen är standard med 60 MiB/S genomströmning<br/>-45 GB data ändras efter redundans| Ungefärlig tid 1 timmar – 2 timmar<br/>-Under återaktiveringen av skyddet Site Recovery fylls kontrollsumman för hela data som tar 127 GB / 45 MB ~ 45 minuter<br/>-Överföra tid för att tillämpa ändringar på 45 GB är 45 GB / 45 Mbit/s ~ 17 minuter<br/>-Kostnader för utgående trafik är bara för 45 GB data inte för kontrollsumman|
 



## <a name="next-steps"></a>Nästa steg

När den virtuella datorn är skyddad, kan du initiera redundans. Redundansen stänger av den virtuella datorn i den sekundära regionen och skapar och startar virtuella datorer i den primära regionen, med vissa små avbrott. Vi rekommenderar att du väljer en tid i enlighet med detta och att du kör ett redundanstest innan du påbörjar en fullständig växling till den primära platsen. [Läs mer](site-recovery-failover.md) om redundans.
