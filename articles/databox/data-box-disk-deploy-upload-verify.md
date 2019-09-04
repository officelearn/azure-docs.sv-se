---
title: Självstudie för att verifiera data uppladdning från Azure Data Box Disk till lagrings konto | Microsoft Docs
description: I den här självstudien får du lära dig hur du verifierar data som laddas upp från Azure Data Box Disk till Azure Storage-kontot.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: a4d814ab5b1f26a6a2b871a850fd5e3153e256f5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240279"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Självstudier: Verifiera data uppladdning från Azure Data Box Disk

Detta är den sista självstudien i serien: Distribuera Azure Data Box Disk. I den här självstudien får du lära dig om:

> [!div class="checklist"]
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra följande [självstudie: Returnera Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När diskarna hämtas upp ändras statusen i portalen till **upphämtad**. Du får också ett spårnings-ID.

![Upphämtade diskar](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

När Microsoft tar emot och genomsöker disken uppdateras statusen till **mottagen**. 

![Mottagna diskar](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

När diskarna ansluts till en server i Azure-datacentret kopieras data automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

![Datakopiering slutförd](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Om kopieringen är klar med fel, se [Felsöka uppladdnings fel](data-box-disk-troubleshoot-upload.md).

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. Dina data kan vara i:

- Ditt Azure Storage konto (er). När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- En eller flera av de hanterade disk resurs grupperna. När du skapar hanterade diskar laddas de virtuella hård diskarna som Page blobbar och konverteras sedan till hanterade diskar. De hanterade diskarna är anslutna till de resurs grupper som anges vid tidpunkten för skapande av order.

  - Om din kopia till hanterade diskar i Azure lyckades, kan du gå till **order informationen** i Azure Portal och anteckna resurs gruppen som angetts för hanterade diskar.

      ![Visa beställnings information](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Gå till den angivna resurs gruppen och leta upp dina hanterade diskar.

      ![Resurs grupp för hanterade diskar](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Om du har kopierat en VHDX eller en dynamisk/differentierad virtuell hård disk överförs VHDX/VHD till mellanlagrings kontot som en Block-Blob. Gå till ditt **lagrings konto > blobbar** och välj sedan lämplig container-StandardSSD, StandardHDD eller PremiumSSD. VHDX/VHD: er bör visas som block blobbar i ditt lagrings konto för lagring.
  
::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När data har överförts till Azure kontrollerar du att dina data finns på lagrings kontona innan du tar bort dem från källan. Dina data kan vara i:

- Ditt Azure Storage konto (er). När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

    - **För block-blobbar och Page blobbar**: https://< storage_account_name >. blob. Core. Windows. net/<containername>/Files/a.txt

    - **För Azure Files**: https://< storage_account_name >. File. Core. Windows. net/<sharename>/Files/a.txt

- En eller flera av de hanterade disk resurs grupperna. När du skapar hanterade diskar laddas de virtuella hård diskarna som Page blobbar och konverteras sedan till hanterade diskar. De hanterade diskarna är anslutna till de resurs grupper som anges vid tidpunkten för skapande av order.

::: zone-end

Gör så här för att kontrol lera att data har överförts till Azure:

1. Öppna det lagringskonto som är kopplat till diskbeställningen.
2. Öppna **Blob Service > Bläddra efter blobar**. Listan över containrar visas. Containrar med samma namn skapas i lagringskontot, motsvarande den undermapp som du skapade under mapparna *BlockBlob* och *PageBlob*.
    Mappnamnen måste följa namngivningskonventionerna för Azure. Annars går det inte att ladda upp data till Azure.

3. Använd Microsoft Azure Storage Explorer och kontrollera att hela datauppsättningen har laddats upp. Bifoga lagrings kontot som motsvarar Data Box Disks ordningen och titta sedan på listan över BLOB-behållare. Välj en container, klicka på **... Mer** och klicka sedan på **Mappstatistik**. I fönstret **Aktiviteter** visas statistiken för mappen, inklusive antal blobar och den totala blobstorleken. Den totala blobstorleken i byte ska stämma med storleken på datauppsättningen.

    ![Mappstatistik i Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Radera data från Data Box-disk

När kopieringen är klar och du har verifierat att data finns i Azure Storage-kontot, raderas på ett säkert sätt med diskarna enligt NIST-standarden.

::: zone target="docs"

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk


Nu kan du gå vidare och titta på hur du hanterar Data Box-diskar via Azure-portalen.

> [!div class="nextstepaction"]
> [Administrera Azure Data Box-disk via Azure-portalen](./data-box-portal-ui-admin.md)

::: zone-end




