---
title: ta med fil
description: ta med fil
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 74fe531c07aa871b06b5d0773f7e8fb0ade80be4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850780"
---
## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv
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

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumeration. Ange ett namn som har minst två, men inte mer än 50 tecken. Namnet måste börja med en bokstav och endast bestå av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj prenumerationen som ska användas. Om du är medlem i endast en prenumeration visas det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (rekommenderas). Det finns flera alternativ endast om ditt arbete eller skola konto är kopplat till flera Azure-prenumeration.
   - **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Om du vill se en lista över tillgängliga resursgrupper i din prenumeration, Välj **Använd befintlig**, och välj sedan en resurs i nedrullningsbara listrutan. Om du vill skapa en ny resursgrupp, Välj **Skapa nytt** och ange namnet. Fullständig information om resursgrupper finns i [översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
   - **Plats**: Välj ett geografiskt område för valvet. Skapa ett valv för att skydda virtuella datorer, valvet **måste** vara i samma region som de virtuella datorerna.

      > [!IMPORTANT]
      > Stäng dialogrutan om du inte är säker på platsen för den virtuella datorn. Gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner kan du skapa ett Recovery Services-valv i varje region. Skapa valvet i den första platsen innan du skapar valv för en annan plats. Det finns behöver inte ange lagringskonton för att lagra säkerhetskopierade data. Recovery Services-valvet och tjänsten Azure Backup hanterar du som automatiskt.
      >
      >

5. När du är redo att skapa Recovery Services-valv väljer **skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund att skapa Recovery Services-valvet. Övervaka Statusmeddelandena i den **meddelanden** område i det övre högra hörnet i portalen. När valvet har skapats är det visas i listan över Recovery Services-valv. Om du inte ser ditt valv, väljer **uppdatera**.

     ![Uppdatera listan över säkerhetskopieringsvalv](./media/backup-create-rs-vault/refresh-button.png)
