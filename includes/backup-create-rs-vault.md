---
title: inkludera fil
description: inkludera fil
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 8586d90631e8d38fa020ff9dab3f626aaedb2760
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003825"
---
## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services valv är en hanterings enhet som lagrar återställnings punkter som skapats med tiden och som tillhandahåller ett gränssnitt för att utföra säkerhets kopierings åtgärder. Dessa inkluderar säkerhets kopieringar på begäran, återställning och skapande av säkerhets kopierings principer.

Följ dessa steg om du vill skapa ett Recovery Services valv.

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

1. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

1. I dialogrutan **Alla tjänster** anger du *Recovery Services*. Listan över resurser filtreras enligt dina inaktuella inaktuella. Välj **Recovery Services valv**i listan över resurser.

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

1. På instrument panelen för **Recovery Services valv** väljer du **Lägg till**.

    ![Lägg till ett Recovery Services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Dialog rutan **Recovery Services valv** öppnas. Ange värden för **namn**, **prenumeration**, **resurs grupp**och **plats**.

    ![Konfigurera Recovery Services-valvet](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst 2 men högst 50 tecken. Namnet måste börja med en bokstav och får bara bestå av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du är medlem i endast en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standard prenumerationen (rekommenderas). Det finns flera alternativ bara om ditt arbets-eller skol konto är associerat med fler än en Azure-prenumeration.
   - **Resurs grupp**: Använd en befintlig resurs grupp eller skapa en ny. Om du vill se en lista över tillgängliga resurs grupper i din prenumeration väljer du **Använd befintlig**och väljer sedan en resurs i list rutan. Om du vill skapa en ny resurs grupp väljer du **Skapa ny** och anger namnet. Mer information om resurs grupper finns i [Azure Resource Manager översikt](../articles/azure-resource-manager/management/overview.md).
   - **Plats**: Välj det geografiska området för valvet. För att skapa ett valv för att skydda data källor *måste* valvet vara i samma region som data källan.

      > [!IMPORTANT]
      > Om du inte är säker på var data källan finns stänger du dialog rutan. Gå till listan över resurser i portalen. Om du har data källor i flera regioner skapar du ett Recovery Services-valv för varje region. Skapa valvet på den första platsen innan du skapar valvet för en annan plats. Du behöver inte ange lagrings konton för att lagra säkerhetskopierade data. Recovery Services valvet och Azure Backup handtaget automatiskt.
      >
      >

1. När du har angett värdena väljer du **Granska + skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/review-and-create.png)

1. När du är redo att skapa Recovery Services-valvet väljer du **skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund att skapa Recovery Services-valvet. Övervaka status meddelanden i **meddelande** fältet i det övre högra hörnet i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv väljer du **Uppdatera**.

     ![Uppdatera listan över säkerhets kopierings valv](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Vi rekommenderar starkt att du granskar standardinställningarna för typ och **säkerhets inställningar** för **lagringsprovider** innan du konfigurerar säkerhets kopieringar i valvet. Mer information finns i avsnittet [Ange redundans för lagring](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) .
