---
title: Självstudie för att skicka Azure Data Box tung tillbaka | Microsoft Docs
description: Lär dig hur du skickar din Azure Data Box tung till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 31185900ede0fae74a04f98eaecee7379fb1c4fe
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427804"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure-preview"></a>Självstudier: Returnera Azure Data Box tung och verifiera ladda upp data till Azure (förhandsversion)


Den här självstudien beskrivs hur du returnerar Azure Data Box tung och verifierar data som överförs till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förbereda för att skicka
> * Leverera Data Box aktiverat till Microsoft
> * Kontrollera datauppladdning till Azure
> * Raderingen av data från Data Box tung

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-heavy-deploy-copy-data.md).
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Leverera Data Box tung tillbaka

1. Kontrollera att enheten är avstängd och alla kablar tas bort. Buffra och på ett säkert sätt placera 4 strömkablar i facket som du kan komma åt från baksidan av enheten.
2. Om enheten är på väg i USA eller Europa, är transportföretaget FedEx.

    1. Ring det lokala numret för ditt transportföretag, och schemalägga för upphämtningen.
    2. Nå ut till [Box dataåtgärder](mailto:DataBoxOps@microsoft.com) att informera om för upphämtningen och för att få returetiketten.
    3. Se till att adressetikett visas på skärmen Rensa fram på enheten.
    4. Kontrollera att de gamla leveransetiketter från föregående leveransen har tagits bort från enheten.
3. När Data Box-tung plockas och genomsöks av din operatör, orderstatus i portalen uppdateringar **hämtas**. Du får också ett spårnings-ID.

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När verifieringen är klar är Data Box-tung ansluten till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

Kontrollera att dina data har överförts till Azure innan du tar bort den från källan. Dina data kan ha:

- Dina Azure Storage-konton. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- Din hanterade disk resursgrupperna. När du skapar hanterade diskar kan de virtuella hårddiskarna överförs som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna är kopplade till resursgrupper som anges vid tidpunkten för skapande av. 

    - Om din kopia till managed disks i Azure går du till den **Order information** i Azure-portalen och gör en anteckning resursgrupper som har angetts för hanterade diskar.

        ![Identifiera resursgrupper för hanterad disk](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Gå till antecknat resursgruppen och leta upp din hanterade diskar.

        ![Hanterad disk som är ansluten till resursgrupper](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Om du har kopierat en vhdx-disk eller en dynamisk/differentierande virtuell Hårddisk har VHDX/VHD överförts till mellanlagringskontot som en sidblobb men konverteringen av VHD till hanterad disk misslyckas. Gå till din mellanlagring **lagringskonto > Blobar** och välj sedan så att rätt behållare – Standard SSD, HDD-Standard eller Premium SSD. De virtuella hårddiskarna laddas upp som sidblobar i din mellanlagringskontot.

## <a name="erasure-of-data-from-data-box-heavy"></a>Raderingen av data från Data Box tung
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). När raderingen är klar kan du [hämta orderhistorik](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förbereda för att skicka
> * Leverera Data Box aktiverat till Microsoft
> * Kontrollera datauppladdning till Azure
> * Raderingen av data från Data Box tung

Gå vidare till följande artikel om hur du hanterar Data Box tung via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)


