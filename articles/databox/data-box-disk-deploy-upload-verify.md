---
title: Självstudie för att verifiera datauppladdning från Azure Data Box Disk till lagringskonto | Microsoft Docs
description: I den här självstudien får du lära dig hur du verifierar data som laddas upp från Azure Data Box Disk till Azure-lagringskontot.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/04/2019
ms.author: alkohli
ms.openlocfilehash: e2851f7087151b09b5f859b7142aa28e5b18a4e5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514093"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Självstudier: Verifiera datauppladdning från Azure Data Box Disk

Detta är den sista självstudien i serien: Distribuera Azure Data Box Disk. I den här självstudien lär du dig att:

> [!div class="checklist"]
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra följande [självstudie: Returnera Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När diskarna hämtas upp ändras statusen i portalen till **upphämtad**. Du får också ett spårnings-ID.

![Upphämtade diskar](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

När Microsoft tar emot och genomsöker disken uppdateras statusen till **mottagen**. 

![Mottagna diskar](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

När diskarna ansluts till en server i Azure-datacentret kopieras data automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

![Datakopiering slutförd](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Om kopieringen slutförs med fel läser du [Felsöka uppladdningsfel](data-box-disk-troubleshoot-upload.md).

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. Dina data kan finnas i:

- Ditt Azure Storage-konto. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- Dina resursgrupper för hanterade diskar. När du skapar hanterade diskar laddas de virtuella hårddiskarna upp som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna ansluts till de resursgrupper som anges när ordern skapas.

  - Om kopieringen till hanterade diskar i Azure lyckades kan du gå till **Beställningsinformation** på Azure-portalen och kontrollera vilka resursgrupper som har angetts för hanterade diskar.

      ![Visa beställningsinformation](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Gå till resursgruppen och leta upp dina hanterade diskar.

      ![Resursgrupp för hanterade diskar](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Om du har kopierat en VHDX, eller en dynamisk/differentierad virtuell hårddisk, överförs VHDX/VHD till mellanlagringskontot som en blockblob. Gå till ditt **mellanlagringskonto > Blobar** och välj sedan lämplig container – Standard SSD, Standard HDD eller Premium SSD. VHDX-/VHD-diskar bör visas som blockblobar i mellanlagringskontot.
  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När data har överförts till Azure kontrollerar du att dina data finns på lagringskontona innan du tar bort dem från källan. Dina data kan finnas i:

- Ditt Azure Storage-konto. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

    - **För blockblobar och sidblobar**: https://<namn_på_lagringskonto>.blob.core.windows.net/<containername>/files/a.txt

    - **För Azure Files**: https://<namn_på_lagringskonto>.file.core.windows.net/<sharename>/files/a.txt

- Dina resursgrupper för hanterade diskar. När du skapar hanterade diskar laddas de virtuella hårddiskarna upp som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna ansluts till de resursgrupper som anges när ordern skapas.

::: zone-end

Så här kontrollerar du att data har överförts till Azure:

1. Öppna det lagringskonto som är kopplat till diskbeställningen.
2. Öppna **Blob Service > Bläddra efter blobar**. Listan över containrar visas. Containrar med samma namn skapas i lagringskontot, motsvarande den undermapp som du skapade under mapparna *BlockBlob* och *PageBlob*.
    Mappnamnen måste följa namngivningskonventionerna för Azure. Annars går det inte att ladda upp data till Azure.

3. Använd Microsoft Azure Storage Explorer och kontrollera att hela datauppsättningen har laddats upp. Bifoga lagringskontot som motsvarar Data Box Disk-beställningen och titta sedan på listan över blobcontainrar. Välj en container, klicka på **... Mer** och klicka sedan på **Mappstatistik**. I fönstret **Aktiviteter** visas statistiken för mappen, inklusive antal blobar och den totala blobstorleken. Den totala blobstorleken i byte ska stämma med storleken på datauppsättningen.

    ![Mappstatistik i Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Radera data från Data Box-disk

När kopieringen har slutförts och du har kontrollerat att alla data finns i Azure-lagringskontot raderas diskarna på ett säkert sätt enligt NIST-standarden.

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




