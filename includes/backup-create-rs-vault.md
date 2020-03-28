---
title: ta med fil
description: ta med fil
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262583"
---
## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en lagringsenhet som lagrar återställningspunkterna som skapats över tid. Den innehåller också de principer för säkerhetskopiering som är associerade med skyddade objekt.

Så här skapar du ett Recovery Services-valv.

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

1. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

1. I dialogrutan **Alla tjänster** anger du *Recovery Services*. Listan över resurser filtrerar enligt dina indata. Välj **Recovery Services-valv**i listan över resurser .

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

1. På instrumentpanelen **för Återställningstjänster** väljer du **Lägg till**.

    ![Lägga till ett recovery services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Dialogrutan **Återställningstjänster** öppnas. Ange värden för **gruppen Namn,** **Prenumeration,** **Resurs**och **Plats**.

    ![Konfigurera valvet för återställningstjänster](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn för att identifiera valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som har minst 2 men inte fler än 50 tecken. Namnet får börja med en bokstav och består endast av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du bara är medlem i en prenumeration visas det namnet. Om du är osäker på vilken prenumeration du ska använda använder du standardprenumerationen (föreslagen). Det finns bara flera alternativ om ditt arbets- eller skolkonto är kopplat till mer än en Azure-prenumeration.
   - **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Om du vill visa listan över tillgängliga resursgrupper i prenumerationen väljer du **Använd befintlig**och väljer sedan en resurs i listrutan. Om du vill skapa en ny resursgrupp väljer du **Skapa ny** och anger namnet. Mer information om resursgrupper finns i [översikt över Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Plats**: Välj det geografiska området för valvet. Om du vill skapa ett valv för att skydda virtuella datorer *måste* valvet finnas i samma region som de virtuella datorerna.

      > [!IMPORTANT]
      > Om du inte är säker på var den virtuella datorn är är stänger du dialogrutan. Gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner skapar du ett recovery services-valv i varje region. Skapa valvet på den första platsen innan du skapar valvet för en annan plats. Du behöver inte ange lagringskonton för att lagra säkerhetskopieringsdata. Valvet för återställningstjänster och Azure Backup som automatiskt.
      >
      >

1. När du är redo att skapa valvet för Återställningstjänster väljer du **Skapa**.

    ![Skapa valvet för återställningstjänster](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta ett tag att skapa valvet för Återställningstjänster. Övervaka statusmeddelanden i området **Meddelanden** längst upp till höger på portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser valvet väljer du **Uppdatera**.

     ![Uppdatera listan över valv för säkerhetskopiering](./media/backup-create-rs-vault/refresh-button.png)
