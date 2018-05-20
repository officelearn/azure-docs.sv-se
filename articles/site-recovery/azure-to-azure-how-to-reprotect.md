---
title: Skyddar redundansväxlade virtuella Azure-datorer tillbaka till den primära regionen som Azure med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du skyddar virtuella Azure-datorer i en sekundär region efter växling från en primär region med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: rajanaki
ms.openlocfilehash: ccec4262297314bad261a852bb5db25c428ce0a0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Skyddar redundansväxlade virtuella Azure-datorer till den primära regionen


>[!NOTE]
>
> Site Recovery-replikering för virtuella Azure-datorer är nu i förhandsversion.



När du [redundansväxla](site-recovery-failover.md) Azure virtuella datorer från en region med en annan [Azure Site Recovery](site-recovery-overview.md), virtuella datorer startas i den sekundära regionen oskyddad. Om misslyckas tillbaka de virtuella datorerna till den primära regionen, måste du göra följande:

- Skyddar virtuella datorer i den sekundära regionen så att de startar replikeras till den primära regionen. 
- Efter att återaktivera skyddet har slutförts och replikerar de virtuella datorerna, kan du växla dem över från sekundärt till primär region.

> [!WARNING]
> Om du har [migreras](migrate-overview.md#what-do-we-mean-by-migration) datorer från den primära servern till den sekundära regionen flyttas den virtuella datorn till en annan resursgrupp eller ta bort den virtuella Azure-datorn kan du inte skyddar den virtuella datorn eller växla tillbaka.


## <a name="prerequisites"></a>Förutsättningar
1. VM-redundans från primär till sekundär region måste genomföras.
2. Den primära målplatsen ska vara tillgängliga och du ska kunna komma åt eller skapa resurser i den regionen.

## <a name="reprotect-a-vm"></a>Skyddar en virtuell dator

1. I **valvet** > **replikerade objekt**, högerklicka på den misslyckade över VM och välj **skydda igen**. Återaktivera skydd riktningen ska visa från sekundär primär. 

  ![Skydda igen](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Granska resurs grupp, nätverk, lagring och tillgänglighet anger. Klicka sedan på **OK**. Om det finns några resurser som är markerade som ny, skapas de som en del av processen återaktivera skydd.
3. Återaktivera skydd jobbet lägger målplatsen med den senaste informationen. Efter som slutförts sker deltareplikering. Sedan kan du växla över till den primära platsen. Du kan välja lagringskontot eller nätverket som du vill använda under skydda igen med alternativet Anpassa.

  ![Anpassa alternativet](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Anpassa inställningar för skydda igen

Du kan anpassa följande egenskaper för målet VMe under återaktivera skydd.

![Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap  |Anteckningar  |
|---------|---------|
|Målresursgruppen     | Ändra målresursgruppen där den virtuella datorn skapas. Målet VM som del av återaktivera skydd, har tagits bort. Du kan välja en ny resursgrupp som du vill skapa den virtuella datorn efter växling vid fel under.        |
|Mål virtuellt nätverk     | Målnätverket kan inte ändras under Skapa nytt jobb. Gör om nätverksmappningen om du vill ändra i nätverket.         |
|Mål-Lagringskontot (sekundära virtuella datorn inte använder hanterade diskar)     | Du kan ändra lagringskontot som den virtuella datorn använder efter växling vid fel.         |
|Replik hanterade diskar (sekundär virtuell dator använder hanterade diskar)    | Site Recovery skapar diskar replik som hanteras i den primära regionen för spegling av hanterade diskar för den sekundära virtuella datorn.         | 
|Cachelagring     | Du kan ange ett cache-lagringskonto som ska användas vid replikering. Som standard är ett nytt lagringskonto för cachen skapas om den inte finns.         |
|Tillgänglighetsuppsättning     |Om den virtuella datorn i den sekundära regionen är en del av en tillgänglighetsuppsättning, kan du välja en tillgänglighetsuppsättning för målet VM i den primära regionen. Site Recovery som standard försöker hitta den befintliga tillgänglighetsuppsättning i den primära regionen och använda den. Vid anpassning, kan du ange en ny tillgänglighetsuppsättning.         |


### <a name="what-happens-during-reprotection"></a>Vad som händer under återaktivera skydd?

Som standard sker följande:

1. Ett cache-lagringskonto skapas i den primära regionen
2. Om mål-lagringskontot (det ursprungliga storage-kontot i den primära regionen) inte finns, skapas en ny. Tilldelade lagringskontonamn är namnet på lagringskontot som används av den sekundära virtuella datorn med ”asr” suffixet.
3. Om den virtuella datorn använder hanterade diskar, replik hanteras diskar skapas i den primära regionen att lagra data som replikeras från den sekundära Virtuella diskar. 
4. Om tillgänglighet måluppsättningen inte finns, skapas en ny som en del av jobbet skydda igen om det behövs. Om du har anpassade inställningar för återaktivera skydd används den markerade uppsättningen.

När du utlösa ett jobb som skyddar och målet VM finns inträffar följande:

1. De nödvändiga komponenterna skapas som en del av skydda igen. Om det redan finns återanvänds.
2. Målsidan VM är inaktiverat om det körs.
3. Måldisken sida VM kopieras av Site Recovery till en behållare som en seed-blob.
4. Målsidan VM raderas sedan.
5. Seed-blob som används av den aktuella källan sida (sekundär) VM att replikera. Detta säkerställer att endast går replikeras.
6. Större ändringar mellan källdisken och seed-blob har synkroniserats. Det kan ta lite tid att slutföra.
7. När skyddar-jobbet slutförs deltareplikeringen börjar och skapar en återställningspunkt i enlighet med replikeringsprincipen.
8. När du skyddar jobb lyckas, försätts den virtuella datorn i ett skyddat läge.

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn är skyddad, kan du påbörja en växling. Växling vid fel stängs den virtuella datorn i den sekundära regionen och skapar och startar VM i den primära regionen, med vissa små avbrott. Vi rekommenderar att du väljer en tid i enlighet med detta och att du kör ett redundanstest men initierar en fullständig växling till den primära platsen. [Lär dig mer](site-recovery-failover.md) om redundans.

