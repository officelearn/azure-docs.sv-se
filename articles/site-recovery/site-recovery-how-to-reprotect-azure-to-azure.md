---
title: "Hur du skyddar från redundansväxlats virtuella Azure-datorer till Azure-region som primär | Microsoft Docs"
description: "Du kan använda Azure Site Recovery efter växling för virtuella datorer från en Azure-region till en annan, för att skydda datorer i omvänd riktning. Lär dig stegen om du vill skydda igen innan en växling vid fel igen."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd
ms.openlocfilehash: 5822ed90f3ab13bdaf1afef62cf32978101c6609
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>Det gick inte att skydda igen från via Azure-region tillbaka till primär region



>[!NOTE]
>
> Site Recovery replikering för virtuella Azure-datorer är för närvarande under förhandsgranskning.


## <a name="overview"></a>Översikt
När du [redundans](site-recovery-failover.md) virtuella datorer från en Azure-region till en annan, de virtuella datorerna är oskyddad. Om du vill få dem tillbaka till den primära regionen måste du först skydda virtuella datorer och växling vid fel igen. Det finns ingen skillnad mellan hur redundans i en riktning eller andra. Bokför på samma sätt kan aktivera skydd för virtuella datorer och det finns ingen skillnad mellan att skydda igen efter växling vid fel eller efter återställning efter fel.
Att förklara arbetsflöden för att skydda igen, och att undvika förvirring, referera till den primära platsen för de skydda datorerna som Östasien region och återställningsplatsen för datorer som Sydostasien region. Under växling vid fel startar de virtuella datorerna i Sydostasien region. Innan du återställning efter fel måste du skyddar virtuella datorer från Sydostasien tillbaka till Östasien. Den här artikeln beskriver stegen för hur du skyddar.

> [!WARNING]
> Om du har [slutfört en migrering](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), flyttas den virtuella datorn till en annan resursgrupp eller ta bort Azure-dator, du kan inte skydda igen eller återställning av den virtuella datorn.

När skyddar är klar och replikerar de skyddade virtuella datorerna, kan du påbörja en växling på de virtuella datorerna för att göra dem Östasien region.

Skicka kommentarer eller frågor i slutet av den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Krav
1. De virtuella datorerna ska ha begåtts.
2. Målplatsen – i det här fallet Östasien Azure-region ska vara tillgängliga och ska kunna åtkomst/skapa nya resurser i den regionen.

## <a name="steps-to-reprotect"></a>Steg för att skydda igen

Nedan följer stegen för att skydda igen en virtuell dator med standardinställningarna.

1. I **valvet** > **replikerade objekt**, högerklicka på den virtuella datorn har växlas över och välj sedan **skydda igen**. Du kan också klicka på datorn och välja **skydda igen** från knapparna.

![Högerklicka om du vill skydda igen](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. I bladet Lägg märke till att riktningen för skydd, **Sydostasien till Östasien**, har redan valts.

![Skapa nytt blad](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Granska de **resursgrupp, nätverk, lagring och tillgänglighet anger** information och klicka på OK. Om det finns några resurser som är markerade (nya), kommer att skapas som en del av att skydda igen.

Detta kommer att utlösa ett jobb skyddar jobb som kommer först att dirigera målplatsen (SEA i det här fallet) med den senaste informationen och när som har slutförts, replikeras innan du redundans går tillbaka till Sydostasien.

### <a name="reprotect-customization"></a>Skapa nytt anpassning
Om du vill välja extrahera storage-konto eller nätverket under skyddar göra du det med alternativet Anpassa på bladet skydda igen.

![Anpassa alternativet](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Du kan anpassa följande egenskaper för han virtuella måldatorn under skydda igen.

![Anpassa blad](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap |Anteckningar  |
|---------|---------|
|Målresursgruppen     | Du kan välja att ändra målresursgruppen th virtuella datorn kommer att skapas. Som del av skyddar, kommer att ta bort den virtuella måldatorn, därför kan du välja en ny resursgrupp som du kan skapa VM efter växling vid fel         |
|Mål virtuellt nätverk     | Nätverket kan inte ändras under skyddar jb. Gör om nätverksmappningen om du vill ändra i nätverket.         |
|Mål-Lagringskontot     | Du kan ändra lagringskontot som den virtuella datorn kommer att skapas efter växling vid fel.         |
|Cachelagring     | Du kan ange ett cache-lagringskonto som ska användas vid replikering. Om du går med standardvärdena skapas ett nytt lagringskonto för cache, om den inte redan finns.         |
|Tillgänglighetsuppsättning     |Om den virtuella datorn i Östasien ingår i en tillgänglighetsuppsättning, kan du välja en tillgänglighetsuppsättning för den virtuella måldatorn i Sydostasien. Standardvärden hittar den befintliga SEA tillgänglighetsuppsättningen och försöker använda den. Vid anpassning, kan du ange en helt ny uppsättning AV.         |


### <a name="what-happens-during-reprotect"></a>Vad som händer under skydda igen?

Precis som när först aktiverar skydd, följande är artefakter som skapas om du använder standardinställningarna.
1. Ett cache-lagringskonto skapas i Östasien region.
2. Om mål-lagringskontot (ursprungliga lagringskontot för den virtuella datorn Sydostasien) inte finns, skapas en ny. Namnet är Östasien virtuella storage-konto med ”asr” suffixet.
3. Om måluppsättningen AV finns inte och standardinställningarna identifiera som behöver skapa en ny uppsättning AV och kommer att skapas som en del av jobbet skydda igen. Om du har anpassat skydda igen, används den valda AV uppsättningen.
4.

Följande är listan över steg som kan inträffar när du utlösa ett jobb som skyddar. Detta är sida virtuella måldatorn finns.

1. Nödvändiga artefakter skapas som en del av skydda igen. Om det redan finns, och sedan återanvänds.
2. Sida (Sydostasien) virtuella måldatorn är först avstängd, om det körs.
3. Den mål på serversidan virtuella datorns disk kopieras av Azure Site Recovery till en behållare som en seed-blob.
4. Virtuella måldatorn på klientsidan så tas bort.
5. Seed-blob används av den aktuella sida (Östasien) virtuella källdatorn för att replikera. Detta säkerställer att endast går replikeras.
6. De viktigaste förändringarna mellan källdisken och seed-blob har synkroniserats. Det kan ta lite tid att slutföra.
7. När jobbet skyddar är klar börjar deltareplikeringen som skapar en återställningspunkt enligt principen.

> [!NOTE]
> Du kan inte skydda på en återställning plan nivå. Du kan bara skydda igen på en per VM-nivå.

När jobbet skyddar lyckas, kommer den virtuella datorn ange ett skyddat läge.

## <a name="next-steps"></a>Nästa steg

Efter att den virtuella datorn har angett ett skyddat läge, kan du påbörja en växling. Redundans stänga av den virtuella datorn i Östasien Azure-region och sedan skapar och starta Sydostasien region virtuell dator. Det finns därför ett mindre driftstopp för programmet. Välj tid för redundans så när ditt program kan tolerera ett driftstopp. Vi rekommenderar att du först testar växling vid fel den virtuella datorn och kontrollera att det kommer korrekt innan du påbörjar en växling vid fel.

-   [Steg för att initiera testa redundans för den virtuella datorn](site-recovery-test-failover-to-azure.md)

-   [Steg för att initiera redundans för den virtuella datorn](site-recovery-failover.md)
