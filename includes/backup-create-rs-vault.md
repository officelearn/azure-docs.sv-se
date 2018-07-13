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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730506"
---
## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv
Ett Recovery Services-valv är en entitet som lagrar säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery Services-valvet innehåller även säkerhetskopieringspolicyerna som är kopplade till de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i den [Azure-portalen](https://portal.azure.com/).
2. I den vänstra menyn och väljer **alla tjänster**.

    ![Välj alternativet för alla tjänster i huvudmenyn](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. I dialogrutan för alla tjänster, Skriv *återställningstjänster*. När du börjar skriva, filtrerar listan över resurser i dina indata. När du ser det väljer **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-create-rs-vault/all-services.png) <br/>

    Listan över Recovery Services-valv i prenumerationen visas.
4. På den **Recovery Services-valv** menyn och välj **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-create-rs-vault/add-button-create-vault.png)

    Den **Recovery Services-valv** menyn öppnas. Uppmanas du att ange information för **namn**, **prenumeration**, **resursgrupp**, och **plats**.

    ![Fönstret ”recovery Services-valv”](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumeration. Skriv ett namn som innehåller minst två, men inte mer än 50 tecken. Namnet måste börja med en bokstav och det får innehålla endast bokstäver, siffror och bindestreck.
6. För **prenumeration**, Välj den prenumeration som du vill använda. Om du är medlem i endast en prenumeration visas det namnet. Om du inte är säker på vilken prenumeration du ska använda gå med standardvärdet (eller föreslås) prenumeration. Det finns flera alternativ endast om ditt arbete eller skola konto är kopplat till flera Azure-prenumerationer.
7. För **resursgrupp** du kan använda en befintlig resursgrupp eller skapa en ny. Om du vill se listan över resursgrupper i din prenumeration, Välj **Använd befintlig**, och klicka på den nedrullningsbara menyn. Om du vill skapa en ny resursgrupp, Välj **Skapa nytt** och Skriv namnet. Fullständig information om resursgrupper finns i [översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. För **plats** väljer du den geografiska regionen för valvet. Om du skapar ett valv för att skydda virtuella datorer, valvet *måste* vara i samma region som de virtuella datorerna.

   > [!IMPORTANT]
   > Om du är osäker på den plats där din virtuella dator finns stänger dialogrutan valvet skapas och gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i olika regioner skapar du ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Det finns behöver inte ange lagringskonton för att lagra säkerhetskopierade data. Recovery Services-valvet och tjänsten Azure Backup hanterar du som automatiskt.
   >
   >

9. När du är redo att skapa Recovery Services-valvet klickar du på **skapa**.

    ![Lista över säkerhetskopieringsvalv](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka Statusmeddelandena i avsnittet meddelanden (området längst upp till höger i portalen). När valvet har skapats visas den i listan över Recovery Services-valv. Om du fortfarande inte ser ditt valv, klickar du på **uppdatera**.

     ![Lista över säkerhetskopieringsvalv](./media/backup-create-rs-vault/refresh-button.png)
