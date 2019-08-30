---
title: Självstudie för att leverera Azure Data Box Heavy | Microsoft Docs
description: Lär dig hur du levererar Azure Data Box Heavy till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: d8d9478441ffe3962f450bed7bfddf2776386617
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164397"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Självstudier: Returnera Azure Data Box Heavy och verifiera data uppladdning till Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Returnera Azure Data Box Heavy och verifiera data uppladdning till Azure

::: zone-end

::: zone target = "docs"

I den här självstudien beskrivs hur du returnerar Azure Data Box Heavy och verifierar de data som överförs till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Förbered för att skicka
> * Leverera Data Box Heavy till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radering av data från Data Box Heavy

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-heavy-deploy-copy-data.md).
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.


## <a name="prepare-to-ship"></a>Förbered för att skicka

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Förbered för att skicka

Innan du förbereder att leverera ska du kontrol lera att kopierings jobben har slutförts.

1. Gå till Förbered för att skicka sida i det lokala webb gränssnittet och starta förberedelsen av fartyget.
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten.

Du är nu redo att leverera tillbaka enheten.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Leverera Data Box Heavy tillbaka

1. Kontrol lera att enheten är avstängd och att alla kablar tas bort. Buffra och placera 4-nätkablarna på ett säkert sätt i det fack som du kan komma åt från bak sidan av enheten.
2. Enheten levererar LTL frakt via FedEx i USA och DHL i EU

    1. Kontakta [data Box-enhet åtgärder](mailto:DataBoxOps@microsoft.com) för att informera om hämtningen och för att hämta etiketten för retur leverans.
    2. Ring upp det lokala numret för transport företaget för att schemalägga upphämtningen.
    3. Se till att leverans etiketten visas på en framträdande plats på försändelsens yttre.
    4. Se till att de gamla skeppnings etiketterna från den föregående leveransen tas bort från enheten.
3. När Data Box Heavy hämtas och genomsöks av din operatör uppdateras order statusen i portalen till att **hämtas**. Du får också ett spårnings-ID.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När verifieringen är klar är Data Box Heavy ansluten till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

Kontrol lera att dina data överförs till Azure innan du tar bort dem från källan. Dina data kan vara i:

- Ditt Azure Storage konto (er). När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- En eller flera av de hanterade disk resurs grupperna. När du skapar hanterade diskar laddas de virtuella hård diskarna som Page blobbar och konverteras sedan till hanterade diskar. De hanterade diskarna är anslutna till de resurs grupper som anges vid tidpunkten för skapande av order. 

    - Om din kopia till hanterade diskar i Azure lyckades, kan du gå till **order informationen** i Azure Portal och anteckna de resurs grupper som har angetts för hanterade diskar.

        ![Identifiera resurs grupper för hanterade diskar](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Gå till den angivna resurs gruppen och leta upp dina hanterade diskar.

        ![Hanterad disk ansluten till resurs grupper](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Om du har kopierat en VHDX eller en dynamisk/differentierad virtuell hård disk överförs VHDX/VHD till lagrings kontot som en sid-BLOB, men konverteringen av en virtuell hård disk till hanterad disk Miss lyckas. Gå till ditt **lagrings konto > blobbar** och välj sedan lämplig container-Standard SSD, Standard HDD eller Premium SSD. De virtuella hård diskarna laddas upp som Page blobbar i ditt lagrings konto för lagring.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När den Data Box Heavy enheten är ansluten till Azure datacenter-nätverket startar data överföringen till Azure automatiskt. Data Box-enhet tjänsten meddelar dig att data kopieringen har slutförts via Azure Portal.

- Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
- Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Radering av data från Data Box Heavy
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). När radering har slutförts kan du [Ladda ned order historiken](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Förutsättningar
> * Förbered för att skicka
> * Leverera Data Box Heavy till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radering av data från Data Box Heavy

Gå vidare till följande artikel om du vill lära dig hur du hanterar Data Box Heavy via det lokala webb gränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


