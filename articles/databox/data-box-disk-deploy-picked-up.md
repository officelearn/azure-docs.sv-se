---
title: Skicka tillbaka Microsoft Azure Data Box-disk| Microsoft Docs
description: I den här självstudien förklarar vi hur du skickar tillbaka Azure Data Box-disken till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f971a1bed0391e809e19ff5bb0508d153319faf4
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094011"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Självstudie: Skicka tillbaka Azure Data Box-disken och verifiera datauppladdning till Azure

Det här är den sista självstudien i serien om att distribuera Azure Data Box-disken. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du se till att du har slutfört [självstudien om att kopiera data till Azure Data Box-disken och verifiera](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Skicka tillbaka Data Box-disken

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan.
3. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller inte finns kan du hämta en ny etikett på Azure-portalen. Öppna **Översikt > Ladda ned adressetikett**. 

    ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Den här åtgärden laddar ned en adressetikett. Se nedan.

    ![Exempel på adressetikett](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Försegla fraktlådan och se till att adressetiketten är väl synlig.
5. Om du returnerar enheten i USA bokar du upphämtning med UPS. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats. Navigera till DHL-webbplatsen för ditt land. Under Våra divisioner klickar du på DHL Express. Välj **Skicka > Skicka online**.

    ![Skicka online med DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
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

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. Så här kontrollerar du att data har överförts till Azure:

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


