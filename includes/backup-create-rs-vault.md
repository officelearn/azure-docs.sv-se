---
title: ta med fil
description: ta med fil
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664963"
---
## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv
Recovery Services-valvet är en entitet som lagrar säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery Services-valvet innehåller även principer för säkerhetskopiering som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på prenumerationen i den [Azure-portalen](https://portal.azure.com/).
2. Välj i den vänstra menyn **alla tjänster**.

    ![Välj alternativet för alla tjänster på huvudmenyn](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. Ange i dialogrutan alla tjänster *återställningstjänster*. När du börjar skriva filtrerar listan över resurser i dina indata. När du ser det välja **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-create-rs-vault/all-services.png) <br/>

    Lista över Recovery Services-valv i prenumerationen visas.
4. På den **Recovery Services-valv** väljer du **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-create-rs-vault/add-button-create-vault.png)

    Den **Recovery Services-valv** menyn öppnas. Uppmanas du att ange information för **namn**, **prenumeration**, **resursgruppen**, och **plats**.

    ![”Recovery Services-valv” fönstret](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två, men inte mer än 50 tecken. Namnet måste börja med en bokstav och kan innehålla endast bokstäver, siffror och bindestreck.
6. För **prenumeration**, Välj den prenumeration som du vill använda. Om du är medlem i en enda prenumeration kommer namnet att visas. Om du inte är säker på vilken prenumeration för att använda med standardvärdet (eller förslag) prenumerationen. Det finns flera alternativ bara om ditt arbete eller skola konto är kopplat till flera Azure-prenumerationer.
7. För **resursgruppen** du kan använda en befintlig resursgrupp eller skapa en ny. Om du vill se listan över resursgrupper i din prenumeration, Välj **använda befintliga**, och klicka på den nedrullningsbara menyn. Om du vill skapa en ny resursgrupp **Skapa nytt** och ange namnet. Mer information om resursgrupper finns i [översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. För **plats** väljer du ett geografiskt område för valvet. Om du skapar ett valv att skydda virtuella datorer, valvet *måste* vara i samma region som de virtuella datorerna.

   > [!IMPORTANT]
   > Om du är osäker på den plats där den virtuella datorn finns Stäng dialogrutan valvet skapas och gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i olika regioner skapar du ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Det finns inget behov av att ange storage-konton för att lagra säkerhetskopierade data. Recovery Services-valvet och Azure Backup-tjänsten ska du hantera som automatiskt.
   >
   >

9. När du är redo att skapa Recovery Services-valvet, klickar du på **skapa**.

    ![Lista över säkerhetskopieringsvalv](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund innan Recovery Services-valvet har skapats. Meddelandena status i avsnittet meddelanden (det övre högra området i portalen). När din valvet har skapats visas den i listan över Recovery Services-valv. Om du fortfarande inte ser ditt valv klickar du på **uppdatera**.

     ![Lista över säkerhetskopieringsvalv](./media/backup-create-rs-vault/refresh-button.png)
