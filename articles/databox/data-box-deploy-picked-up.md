---
title: Självstudie för att skicka Azure Data Box | Microsoft Docs
description: Lär dig hur du skickar Azure Data Box-enheten till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 5b78dc8b815802502cda5baf2bf5e9646922d671
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70241436"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box och verifiera datauppladdning till Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Skicka tillbaka Data Box och verifiera datauppladdningen till Azure

::: zone-end

::: zone target="docs"

Den här självstudien beskriver hur du returnerar Azure Data Box och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md). 
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

När datakopieringen är klar förbereder du och skickar enheten. När enheten kommer fram till Azures datacenter laddas dina data automatiskt upp till Azure.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Innan du förbereder leveransen kontrollerar du att kopieringsjobben har slutförts.

1. Gå till sidan **Prepare to ship** (Förbered för att skicka)i det lokala webbgränssnittet och börja förbereda frakten. 
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten. 

Nästa steg beror på var du returnerar enheten.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

Kontrollera att datakopieringen till enheten är klar och att **Förbered för att skicka** lyckades. Stegen skiljer sig beroende på i vilken region du skickar enheten.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Skicka i USA, Kanada eller Europa

Utför följande steg om du returnerar enheten i USA, Kanada eller Europa.

1. Kontrollera att enheten är avstängd och att kablarna har tagits bort. 
2. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, tappas bort eller inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Om supporten föreslår det kan du gå till **Översikt > Ladda ned adressetikett** på Azure-portalen. Ladda ned en adressetikett och fäst den på enheten. 
4. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger du en upphämtning:

    - Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
    - Uppge spårningsnumret för returförsändelsen som visas på skärmen E-ink (E-bläck) eller på den utskrivna etiketten.
    - Om du inte uppger spårningsnumret kräver UPS att du betalar en extra avgift vid upphämtningen.

    I stället för att schemalägga upphämtningen kan du även lämna in Data Box-enheten på närmaste inlämningsställe.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.


## <a name="ship-in-australia"></a>Leveranser i Australien

Azure-datacenter i Australien kräver ytterligare en säkerhetsavisering. För alla inkommande leveranser krävs en särskild avisering. Följ stegen nedan vid leveranser i Australien.


1. Returnera enheten i originalkartongen.
2. Kontrollera att datakopieringen till enheten är klar och att **Förbered för att skicka** lyckades.
3. Stäng av enheten och avlägsna kablarna.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
5. Skicka ett e-postmeddelande till Quantium Solutions och be om upphämtning. Uppge tjänstreferensnumret som anges på Azure-portalen. Använd följande e-postmall: *Request for reverse shipping label with TAU code* (Begäran om returfraktsedel med TAU-kod). Inkludera följande information i e-postmeddelandet: 

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
6. Quantium Solutions Australia skickar en returfraktsedel via e-post.
7. Skriv ut returfraktsedeln och fäst den på leveranskartongen.
8. Lämna paketet till fraktföretaget.

Om det behövs kan du kontakta Quantium Solutions support på e-postadressen Azure@quantiumsolutions.com eller via telefon.

För frågor om din beställning via telefonen:

- Skicka ett e-postmeddelande om upphämtning först.
- Ange namnet på din order på telefonen.

## <a name="ship-in-japan"></a>Leveranser i Japan 

1. Returnera enheten i originalkartongen.
2. Stäng av enheten och avlägsna kablarna.
3. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
4. Skriv företagets namn och adressuppgifter på fraktsedeln som din avsändarinformation.
5. Skicka ett e-postmeddelande till Quantium Solutions baserat på följande e-postmall.

    - Om Chakubarai-fraktsedeln från Japan Post inte inkluderades eller saknas, nämner du det i e-postmeddelandet. Quantium Solutions Japan ber Japan Post att ta med fraktsedeln vid upphämtningen.
    - Om du har flera beställningar som ska hämtas upp separat meddelar du detta via e-post.

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

3. Se till att du får en e-postbekräftelse från Quantium Solutions när du har bokat en upphämtning. E-postbekräftelsen innehåller även information om Chakubarai-fraktsedeln.

Vid behov kan du kontakta Quantium Solutions support (på japanska) med följande information: 

- E-postadress: Customerservice.JP@quantiumsolutions.com 
- Telefon：03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När kontrollen har slutförts ansluts Data Box till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

Försäkra dig om att dina data har överförts till Azure innan du tar bort dem från källan. Dina data kan finnas i:

- Ditt Azure Storage-konto. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- Dina resursgrupper för hanterade diskar. När du skapar hanterade diskar laddas de virtuella hårddiskarna upp som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna ansluts till de resursgrupper som anges när ordern skapas. 

    - Om kopieringen till hanterade diskar i Azure lyckades kan du gå till **Beställningsinformation** på Azure-portalen och kontrollera vilka resursgrupper som har angetts för hanterade diskar.

        ![Identifiera resursgrupper för hanterade diskar](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Gå till resursgruppen och leta upp dina hanterade diskar.

        ![Hanterad disk ansluten till resursgrupper](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Om du har kopierat en VHDX, eller en dynamisk/differentierad virtuell hårddisk, överförs VHDX/VHD till mellanlagringskontot som en sidblob, men konverteringen av de virtuella hårddiskarna till hanterade diskar misslyckas. Gå till ditt **mellanlagringskonto > Blobar** och välj sedan lämplig container – Standard SSD, Standard HDD eller Premium SSD. De virtuella hårddiskarna laddas upp som sidblobar i mellanlagringskontot.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

Gå vidare till följande artikeln om hur du hanterar Data Box via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


