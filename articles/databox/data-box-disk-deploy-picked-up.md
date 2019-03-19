---
title: Skicka tillbaka Microsoft Azure Data Box-disk| Microsoft Docs
description: I den här självstudien förklarar vi hur du skickar tillbaka Azure Data Box-disken till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 0dd0474ad1ad360fd82cfdf746d2e9837f74833a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108383"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box-disken och verifiera datauppladdning till Azure

Detta är den sista självstudien i serien: Distribuera Azure Data Box Disk. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra följande [självstudie: Kopiera data till en Azure Data Box-disk och verifiera](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Skicka tillbaka Data Box-disken

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan.
3. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller inte finns kan du hämta en ny etikett på Azure-portalen. Öppna **Översikt > Ladda ned adressetikett**.

    ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Den här åtgärden laddar ned en adressetikett. Se nedan.

    ![Exempel på adressetikett](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Försegla fraktlådan och se till att adressetiketten är väl synlig.
5. Om du returnerar enheten i USA bokar du upphämtning med UPS. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats. Navigera till DHL-webbplatsen för ditt land. Under Våra divisioner klickar du på DHL Express. Välj **Skicka > Skicka online**.

    ![DHL returleverans](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Identifiera ditt fraktsedelsnummer och klicka på **Boka upphämtning**.

      ![Boka upphämtning](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. När diskarna hämtas upp ändras statusen i portalen till **upphämtad**. Du får också ett spårnings-ID.

    ![Upphämtade diskar](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker disken uppdateras statusen till **mottagen**. 

![Mottagna diskar](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

När diskarna ansluts till en server i Azure-datacentret kopieras data automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

![Datakopiering slutförd](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. Dina data kan ha:

- Dina Azure Storage-konton. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- Din hanterade disk resursgrupperna. När du skapar hanterade diskar kan de virtuella hårddiskarna överförs som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna är kopplade till resursgrupper som anges vid tidpunkten för skapande av.

  - Om din kopia till managed disks i Azure går du till den **Order information** i Azure-portalen och gör en anteckning på resursgruppen som angetts för hanterade diskar.

      ![Visa beställningsinformation](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Gå till antecknat resursgruppen och leta upp din hanterade diskar.

      ![Resursgrupp för hanterade diskar](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Om du har kopierat en vhdx-disk eller en dynamisk/differentierande virtuell Hårddisk har i VHDX/VHD överförts till mellanlagringskontot som en blockblob. Gå till din mellanlagring **lagringskonto > Blobar** och välj sedan så att rätt behållare - StandardSSD, StandardHDD eller PremiumSSD. De VHDX/virtuella hårddiskarna ska visas som blockblobar i din mellanlagringskontot.

Så här kontrollerar du att data har överförts till Azure:

1. Öppna det lagringskonto som är kopplat till diskbeställningen.
2. Öppna **Blob Service > Bläddra efter blobar**. Listan över containrar visas. Containrar med samma namn skapas i lagringskontot, motsvarande den undermapp som du skapade under mapparna *BlockBlob* och *PageBlob*.
    Mappnamnen måste följa namngivningskonventionerna för Azure. Annars går det inte att ladda upp data till Azure.

4. Använd Microsoft Azure Storage Explorer och kontrollera att hela datauppsättningen har laddats upp. Bifoga lagringskontot som motsvarar diskbeställningen och titta sedan på listan över blobcontainrar. Välj en container, klicka på **... Mer** och klicka sedan på **Mappstatistik**. I fönstret **Aktiviteter** visas statistiken för mappen, inklusive antal blobar och den totala blobstorleken. Den totala blobstorleken i byte ska stämma med storleken på datauppsättningen.

    ![Mappstatistik i Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Radera data från Data Box-disk

När kopieringen har slutförts och du har kontrollerat att alla data finns i Azure-lagringskontot raderas diskarna på ett säkert sätt enligt NIST-standarden.

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk


Nu kan du gå vidare och titta på hur du hanterar Data Box-diskar via Azure-portalen.

> [!div class="nextstepaction"]
> [Administrera Azure Data Box-disk via Azure-portalen](./data-box-portal-ui-admin.md)


