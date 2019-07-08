---
title: Självstudie för att skicka Azure Data Box tillbaka | Microsoft Docs
description: Lär dig hur du skickar Azure Data Box-enheten till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 7/08/2019
ms.author: alkohli
ms.openlocfilehash: db0f0ac3073687b7c1cd8ca60e459e4bb3aa03f4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626362"
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

Se till att Datakopieringen till enheten har slutförts och **Förbered för att skicka** körningen har slutförts. Utifrån den region där du levererar enheten är proceduren olika.


### <a name="ship-in-us-canada-europe"></a>Leverera i USA, Kanada, Europa

Gör följande om Returnerar enheten i USA, Kanada eller Europa.

1. Se till att enheten är avstängd och kablar tas bort. 
2. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, tappas bort eller inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Om Support föreslår så går du till **översikt > ladda ned adressetikett** i Azure-portalen. Ladda ned en adressetikett och fäst den på enheten. 
4. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger en hämtning:

    - Anropa den lokala UPS (land/region-specifika kostnadsfritt nummer).
    - Citera omvänd leveransen spårnings-ID som visas i E-ink-skärmen eller din utskrivna etikett i dina anrop.
    - Om du inte är av citattecken Spårningsnumret, kräver UPS att betala en extra avgift under hämtning.

    I stället för schemaläggning för upphämtningen släpper du ut den närmaste samlingsplats Data Box.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

### <a name="ship-in-asia-pacific-region"></a>Leverera i Asien / Stillahavsområdet

#### <a name="ship-in-australia"></a>Leverera i Australien

Azure-datacenter i Australien har ett meddelande om ytterligare säkerhet. Alla inkommande leveranser måste ha en meddelanden i förväg. Vidta följande åtgärder för att leverera i Australien.


1. Behåll den ursprungliga box som används för att skicka enhet för returförsändelse.
2. Se till att Datakopieringen till enheten har slutförts och **Förbered för att skicka kör** lyckas.
3. Stänga av enheten och ta bort kablarna.
4. Buffra och på ett säkert sätt placera kontakten som följde med enheten på baksidan av enheten.
5. E-Quantium lösningar begära hämtning. Referera till tjänsten referens värdet som anges på Azure portal. Använd följande postmall för e-:- *förfrågan om omvänd adressetikett med TAU kod*. Se till att inkludera följande information i e-postmeddelandet: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium lösningar Australien e en returetikett.
7. Skriva ut returetiketten och lägger till den på rutan leverans.
8. Överlämna paketet till bud.

Om det behövs kan du e-Quantium lösningssupport Azure@quantiumsolutions.com eller telefon.


För förfrågan om din beställning via telefon:

- Skicka ett e-postmeddelande för upphämtning först.
- Ange namnet på din order på telefonen.

#### <a name="ship-in-japan"></a>Leverera i Japan 

1. Behåll den ursprungliga box som används för att skicka enhet för returförsändelse.
2. Stänga av enheten och ta bort kablarna.
3. Buffra och på ett säkert sätt placera kontakten som följde med enheten på baksidan av enheten.
4. Skriv ditt företag namn och adress information på sändningen kommentaren som denna information.
5. E-Quantium lösning med hjälp av följande e-postmallen.

    - Om Japan Post Chakubarai sändningen kommentar inkluderades eller saknas, Tänk på att i det här e-post. Quantium lösningar Japan begär Japan Post att sändningen Obs vid hämtning.
    - Om du har flera order, e-postmeddelande för att säkerställa att enskilda hämtning.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Få en e-postbekräftelse från Quantium lösningar när du har bokade hämtning. E-postbekräftelse innehåller också information om Chakubarai sändningen anteckningen.

Om det behövs kan du kontakta Quantium lösningssupport (japanska) på följande information: 

- E-postadress:Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 


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


