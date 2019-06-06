---
title: 'Azure Backup: Skapa Recovery Services-valv'
description: Skapa Recovery Services-valv som lagrar säkerhetskopior och återställningspunkter
services: backup
author: sogup
manager: vijayts
keywords: Recovery Services-valv; Azure VM-säkerhetskopiering; Återställning av Azure virtuella datorer;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: 9fba7d679b7d0edb3c99207c99b23f9616c6fa0e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66477586"
---
# <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en entitet som lagrar säkerhetskopior och återställningspunkter som skapats med tiden. Recovery Services-valvet innehåller även säkerhetskopieringspolicyerna som är kopplade till de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Det filtrerar listan över resurser enligt dina indata. Välj i listan över resurser, **Recovery Services-valv**.

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. På den **Recovery Services-valv** instrumentpanelen, väljer **Lägg till**.

    ![Lägg till ett Recovery Services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Den **Recovery Services-valv** öppnas dialogrutan. Ange värden för den **namn**, **prenumeration**, **resursgrupp**, och **plats**.

    ![Konfigurera Recovery Services-valvet](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn på**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumeration. Ange ett namn som har minst två, men inte mer än 50 tecken. Namnet måste börja med en bokstav och endast bestå av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj prenumerationen som ska användas. Om du är medlem i endast en prenumeration visas det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (rekommenderas). Det finns flera alternativ endast om ditt arbete eller skola konto är kopplat till flera Azure-prenumeration.
   - **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Om du vill se en lista över tillgängliga resursgrupper i din prenumeration, Välj **Använd befintlig**, och välj sedan en resurs i nedrullningsbara listrutan. Om du vill skapa en ny resursgrupp, Välj **Skapa nytt** och ange namnet. Fullständig information om resursgrupper finns i [översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Plats**: Välj ett geografiskt område för valvet. Skapa ett valv för att skydda virtuella datorer, valvet **måste** vara i samma region som de virtuella datorerna.

      > [!IMPORTANT]
      > Stäng dialogrutan om du inte är säker på platsen för den virtuella datorn. Gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner kan du skapa ett Recovery Services-valv i varje region. Skapa valvet i den första platsen innan du skapar valv för en annan plats. Det finns behöver inte ange lagringskonton för att lagra säkerhetskopierade data. Recovery Services-valvet och tjänsten Azure Backup hanterar du som automatiskt.
      >
      >

5. När du är redo att skapa Recovery Services-valv väljer **skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund att skapa Recovery Services-valvet. Övervaka Statusmeddelandena i den **meddelanden** område i det övre högra hörnet i portalen. När valvet har skapats är det visas i listan över Recovery Services-valv. Om du inte ser ditt valv, väljer **uppdatera**.

     ![Uppdatera listan över säkerhetskopieringsvalv](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ange lagringsredundans

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur den lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Under den **inställningar** klickar du på **egenskaper**.
2. I **egenskaper**under **Säkerhetskopieringskonfigurationen**, klickar du på **uppdatering**.

3. Välj lagringstyp för replikering och klicka på **spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Vi rekommenderar som om du använder Azure som en slutpunkt för primär lagring av säkerhetskopior fortsätter att använda standard **Geo-redundant** inställningen.
   - Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
   - Läs mer om [geo](../storage/common/storage-redundancy-grs.md) och [lokala](../storage/common/storage-redundancy-lrs.md) redundans.

> [!NOTE]
> Ändra **Storage replikeringstyp** (lokalt redundant / geografiskt redundant) för ett Recovery services-valv måste göras innan du konfigurerar säkerhetskopieringar i valvet. När du konfigurerar säkerhetskopiering, möjlighet att ändra är inaktiverat och du kan inte ändra den **Storage replikeringstyp**. 

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services-valv.
[Lär dig mer om](backup-azure-delete-vault.md) ta bort Recovery Services-valv.
