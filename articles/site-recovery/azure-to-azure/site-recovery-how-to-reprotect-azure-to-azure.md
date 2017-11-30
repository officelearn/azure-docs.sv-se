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
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>Skyddar virtuella datorer i Azure tillbaka till den primära regionen



>[!NOTE]
>
> Site Recovery replikering för virtuella Azure-datorer (VM) är för närvarande under förhandsgranskning.


När du [växla över](../site-recovery-failover.md) virtuella datorer från en Azure-region till en annan, den redundansväxlade virtuella datorer är oskyddad. Om du vill få dem tillbaka till den primära regionen måste du först starta replikering av virtuella datorer och växlar sedan över igen. Det finns ingen skillnad i redundans i en riktning eller andra. På liknande sätt, när du har aktiverat replikering av Virtuella det finns ingen skillnad mellan att återaktivera skydd efter en redundansväxling eller efter återställning efter fel.

Som förklarar hur återaktivera skydd som exempel antar vi att den primära platsen på de skyddade virtuella datorerna är Östasien region och återställningsplatsen Sydostasien. Under växling vid fel växlar över de virtuella datorerna till Sydostasien region. Innan du växlar tillbaka måste replikera de virtuella datorerna från Sydostasien, tillbaka till Östasien. Den här artikeln beskriver de steg som krävs för att återaktivera skydd.

> [!WARNING]
> Om du har slutfört en migrering och flytta den virtuella datorn till en annan resursgrupp (eller ta bort den virtuella Azure-datorn) kan inte du återställas.

Efter att återaktivera skyddet har slutförts, och replikerar de virtuella datorerna, kan du påbörja en växling på virtuella datorer, så att de tillbaka till Östasien region.

## <a name="prerequisites"></a>Krav
1. Den virtuella datorn bör har utförts.
2. Målplatsen (Östasien) ska vara tillgängliga och du ska kunna åtkomst/skapa nya resurser i den regionen.

## <a name="reprotect"></a>Skydda igen

Följ dessa steg om du vill skydda igen en virtuell dator med hjälp av standardinställningarna.

1. I **valvet** > **replikerade objekt**, högerklicka på den virtuella datorn som redundansväxling och välj sedan **skydda igen**. Du kan också klicka på datorn och välja **skydda igen** från knapparna.

  ![Återaktivera skydd](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Kontrollera att riktning på replikeringen **Sydostasien till Östasien**, väljs.

  ![Skydda igen](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Granska de **resursgrupp, nätverk, lagring och tillgänglighet anger** information och klickar på **OK**. Om det finns några resurser som är markerade (nya), skapas de under återaktivera skydd.

Detta utlöser ett återaktivera skydd jobb som dirigerar målplatsen med senaste data, och när det är klart replikerar går innan du växlar tillbaka till Sydostasien.

### <a name="reprotect-customization"></a>Skapa nytt anpassning
Om du vill välja extrahera storage-konto eller nätverket under skydda igen, gör du det med hjälp av alternativet Anpassa på sidan återaktivera skydd.

![Anpassa alternativet](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Du kan anpassa följande egenskaper för den virtuella måldatorn under återaktivera skydd.

![Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap |Anteckningar  |
|---------|---------|
|Målresursgruppen     | Du kan välja att ändra målresursgruppen th virtuella datorn kommer att skapas. Som del av skyddar, kommer att ta bort den virtuella måldatorn, därför kan du välja en ny resursgrupp som du kan skapa VM efter växling vid fel         |
|Mål virtuellt nätverk     | Nätverket kan inte ändras under återaktivera skydd. Gör om nätverksmappningen om du vill ändra i nätverket.         |
|Mål-Lagringskontot     | Du kan ändra lagringskontot som den virtuella datorn kommer att skapas efter växling vid fel.         |
|Cachelagring     | Du kan ange ett cache-lagringskonto som ska användas vid replikering. Om du går med standardvärdena skapas ett nytt lagringskonto för cache, om den inte redan finns.         |
|Tillgänglighetsuppsättning     |Om den virtuella datorn i Östasien ingår i en tillgänglighetsuppsättning, kan du välja en tillgänglighetsuppsättning för den virtuella måldatorn i Sydostasien. Standardvärden hittar den befintliga SEA tillgänglighetsuppsättningen och försöker använda den. Vid anpassning, kan du ange en helt ny uppsättning AV.         |


### <a name="what-happens-during-reprotect"></a>Vad som händer under skydda igen?

Precis som när först aktiverar skydd, följande är artefakter som skapas om du använder standardinställningarna.
1. Ett cache-lagringskonto skapas i Östasien region.
2. Om mål-lagringskontot (ursprungliga lagringskontot för den virtuella datorn Sydostasien) inte finns, skapas en ny. Namnet är Östasien virtuella storage-konto med ”asr” suffixet.
3. Om måluppsättningen AV finns inte och standardinställningarna identifiera som behöver skapa en ny uppsättning AV och kommer att skapas som en del av jobbet återaktivera skydd. Om du har anpassat återaktivera skydd används den valda AV uppsättningen.


Följande är listan över steg som kan inträffar när du utlösa ett jobb för återaktivera skydd. Detta är sida virtuella måldatorn finns.

1. Nödvändiga artefakter skapas som en del av skydda igen. Om det redan finns, och sedan återanvänds.
2. Sida (Sydostasien) virtuella måldatorn är först avstängd, om det körs.
3. Den mål på serversidan virtuella datorns disk kopieras av Azure Site Recovery till en behållare som en seed-blob.
4. Virtuella måldatorn på klientsidan så tas bort.
5. Seed-blob används av den aktuella sida (Östasien) virtuella källdatorn för att replikera. Detta säkerställer att endast går replikeras.
6. De viktigaste förändringarna mellan källdisken och seed-blob har synkroniserats. Det kan ta lite tid att slutföra.
7. När jobbet återaktivera skydd är klar börjar deltareplikeringen som skapar en återställningspunkt enligt principen.

> [!NOTE]
> Du kan inte skydda på en återställning plan nivå. Du kan bara skydda igen på en per VM-nivå.

När det återaktivera skyddet lyckas, kommer den virtuella datorn anger ett skyddat läge.

## <a name="next-steps"></a>Nästa steg

Efter att den virtuella datorn har angett ett skyddat läge, kan du påbörja en växling. Redundans stänga av den virtuella datorn i Östasien Azure-region och sedan skapar och starta Sydostasien region virtuell dator. Det finns därför ett mindre driftstopp för programmet. Välj tid för redundans så när ditt program kan tolerera ett driftstopp. Vi rekommenderar att du kör ett redundanstest för den virtuella datorn först för att se till att det kommer korrekt innan du påbörjar en växling vid fel.

-   [Steg för att initiera testa redundans för den virtuella datorn](../site-recovery-test-failover-to-azure.md)

-   [Steg för att initiera redundans för den virtuella datorn](../site-recovery-failover.md)
