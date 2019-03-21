---
title: Skicka tillbaka Microsoft Azure Data Box| Microsoft Docs
description: Lär dig hur du skickar Azure Data Box-enheten till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 72d6ce58a986ddd0d0976d99de5ca3426d78f0b9
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287170"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box och verifiera datauppladdning till Azure

Den här självstudien beskriver hur du returnerar Azure Data Box och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md). 
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

1. Kontrollera att enheten är avstängd och kablarna har tagits bort. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
2. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, tappas bort eller inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Om Support föreslår så går du till **översikt > ladda ned adressetikett** i Azure-portalen. Ladda ned en adressetikett och fäst den på enheten. 
3. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger en hämtning:

    - Anropa den lokala UPS (landsspecifika kostnadsfritt nummer).
    - Citera omvänd leveransen spårnings-ID som visas i E-ink-skärmen eller din utskrivna etikett i dina anrop.
    - Om du inte är av citattecken Spårningsnumret, kräver UPS att betala en extra avgift under hämtning.

    I stället för schemaläggning för upphämtningen släpper du ut den närmaste samlingsplats Data Box.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När kontrollen har slutförts ansluts Data Box till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

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

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Förutsättningar
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

Gå vidare till följande artikeln om hur du hanterar Data Box via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)


