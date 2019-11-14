---
title: 'Azure Backup: skapa Recovery Services-valv'
description: I den här artikeln lär du dig hur du skapar Recovery Services valv som lagrar säkerhets kopior och återställnings punkter.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
keywords: Recovery Services valv; Azure VM-säkerhetskopiering; Återställning av Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: dacurwin
ms.openlocfilehash: 30e2f8812b14f27b57ef22e30aea89f19fa56e58
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074885"
---
# <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Ett Recovery Services-valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Recovery Services valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Listan över resurser filtreras enligt dina inaktuella inaktuella. Välj **Recovery Services valv**i listan över resurser.

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. På instrument panelen för **Recovery Services valv** väljer du **Lägg till**.

    ![Lägg till ett Recovery Services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Dialog rutan **Recovery Services valv** öppnas. Ange värden för **namn**, **prenumeration**, **resurs grupp**och **plats**.

    ![Konfigurera Recovery Services-valvet](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två, men högst 50 tecken. Namnet måste börja med en bokstav och får bara bestå av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du är medlem i endast en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standard prenumerationen (rekommenderas). Det finns flera alternativ bara om ditt arbets-eller skol konto är associerat med fler än en Azure-prenumeration.
   - **Resurs grupp**: Använd en befintlig resurs grupp eller skapa en ny. Om du vill se en lista över tillgängliga resurs grupper i din prenumeration väljer du **Använd befintlig**och väljer sedan en resurs i list rutan. Om du vill skapa en ny resurs grupp väljer du **Skapa ny** och anger namnet. Fullständig information om resurs grupper finns i [Azure Resource Manager översikt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Plats**: Välj det geografiska området för valvet. För att skapa ett valv för att skydda virtuella datorer **måste** valvet vara i samma region som de virtuella datorerna.

      > [!IMPORTANT]
      > Om du inte är säker på var din virtuella dator finns stänger du dialog rutan. Gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner skapar du ett Recovery Services valv i varje region. Skapa valvet på den första platsen innan du skapar valvet för en annan plats. Du behöver inte ange lagrings konton för att lagra säkerhetskopierade data. Recovery Services valvet och Azure Backups tjänstens referens som automatiskt.
      >
      >

5. När du är redo att skapa Recovery Services-valvet väljer du **skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund att skapa Recovery Services-valvet. Övervaka status meddelanden i **meddelande** fältet i det övre högra hörnet i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv väljer du **Uppdatera**.

     ![Uppdatera listan över säkerhets kopierings valv](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ange redundans för lagring

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Under avsnittet **Inställningar** klickar du på **Egenskaper**.
2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.

3. Välj typ av lagrings replikering och klicka på **Spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Vi rekommenderar att om du använder Azure som primär slut punkt för lagring av säkerhets kopior fortsätter du att använda standardvärdet **Geo-redundant** .
   - Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
   - Lär dig mer om [geo](../storage/common/storage-redundancy-grs.md) och [lokal](../storage/common/storage-redundancy-lrs.md) redundans.

> [!NOTE]
> Att ändra **typ av lagrings replikering** (lokalt redundant/Geo-redundant) för ett Recovery Services-valv måste göras innan du konfigurerar säkerhets kopieringar i valvet. När du har konfigurerat säkerhets kopiering inaktive ras alternativet att ändra och du kan inte ändra **typen av lagrings replik**.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services valv.
[Läs mer om](backup-azure-delete-vault.md) Ta bort Recovery Services valv.
