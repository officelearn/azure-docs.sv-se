---
title: Självstudie för att leverera Azure Data Box | Microsoft Docs
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
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241436"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box och verifiera datauppladdning till Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Returnera Data Box-enhet och verifiera data uppladdning till Azure

::: zone-end

::: zone target="docs"

Den här självstudien beskriver hur du returnerar Azure Data Box och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Förbered för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md). 
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbered för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

När data kopieringen är klar förbereder du och levererar enheten. När enheten når Azure-datacenter överförs data automatiskt till Azure.

## <a name="prepare-to-ship"></a>Förbered för att skicka

Innan du förbereder att leverera ska du kontrol lera att kopierings jobben har slutförts.

1. Gå till sidan **Prepare to ship** (Förbered för att skicka)i det lokala webbgränssnittet och börja förbereda frakten. 
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten. 

Nästa steg bestäms av var du returnerar enheten.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

Kontrol lera att data kopieringen till enheten är klar och att **Förbered för att skicka** körningen har slutförts. Proceduren skiljer sig från den region där du levererar enheten.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Leverera i USA, Kanada, Europa

Utför följande steg om du returnerar enheten i USA, Kanada eller Europa.

1. Kontrol lera att enheten är avstängd och att kablarna har tagits bort. 
2. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
3. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, tappas bort eller inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Om support förslaget föreslår det kan du gå till **översikt > Ladda ned frakt etikett** i Azure Portal. Ladda ned en adressetikett och fäst den på enheten. 
4. Om du returnerar enheten bokar du upphämtning med UPS. Så här schemalägger du en upphämtning:

    - Ring den lokala UPS-enheten (lands-/regionsspecifika avgiftsfritt nummer).
    - I ditt samtal offerterar du det omvända försändelse spårnings numret som visas i visningen av E-pennan teckningar eller den utskrivna etiketten.
    - Om spårnings numret inte anges kräver UPS att du betalar ytterligare en kostnad under upphämtningen.

    I stället för att schemalägga upphämtningen kan du också ta bort Data Box-enhet på den närmaste List rutan.
4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.


## <a name="ship-in-australia"></a>Skeppa i Australien

Azure-datacenter i Australien har ytterligare en säkerhets avisering. Alla inkommande leveranser måste ha en avancerad avisering. Utför följande steg för att leverera i Australien.


1. Behåll den ursprungliga rutan som används för att leverera enheten för retur leverans.
2. Kontrol lera att data kopieringen till enheten är klar och att **Förbered för att skicka körningen** har slutförts.
3. Stäng av enheten och ta bort kablarna.
4. Buffra och placera ström sladden på ett säkert sätt med enheten på enhetens bak enhet.
5. Quantium-lösningar för att begära en upphämtning. Referera till det tjänst referens nummer som anges på Azure Portal. Använd följande e-postmall:- *begäran om omvänd leverans etikett med tau-kod*. Se till att ta med följande information i e-postmeddelandet: 

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
6. Quantium-lösningar i Australien får du en e-postadress till en retur frakt etikett.
7. Skriv ut retur etiketten och fäst den i leverans rutan.
8. Handen över paketet till courier.

Om det behövs kan du e-Quantium lösnings Azure@quantiumsolutions.com support på eller via telefon.

För fråga om din beställning via telefonen:

- Skicka ett e-postmeddelande för hämtning först.
- Ange ditt beställnings namn på telefonen.

## <a name="ship-in-japan"></a>Leverera i Japan 

1. Behåll den ursprungliga rutan som används för att leverera enheten för retur leverans.
2. Stäng av enheten och ta bort kablarna.
3. Buffra och placera ström sladden på ett säkert sätt med enheten på enhetens bak enhet.
4. Skriv företagets namn och adress information på frakt sedeln som avsändar information.
5. Quantium-lösning för e-post med hjälp av följande e-postmall.

    - Om Japan post Chakubarai frakt NOTE inte ingår eller saknas, Observera i det här e-postmeddelandet. Quantium-lösningar i Japan kommer att begära en japansk post för att ta frakt sedeln vid upphämtning.
    - Om du har flera order, e-post för att se till att enskilda upphämtningar.

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

3. Få en bekräftelse via e-post från Quantium-lösningar när du har bokat en hämtning. Bekräftelse av e-post innehåller också information om Chakubarai frakt Note.

Vid behov kan du kontakta Quantium Solution support (japanska språk) med följande information: 

- ÅtkomstCustomerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När kontrollen har slutförts ansluts Data Box till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

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
> * Förutsättningar
> * Förbered för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

Gå vidare till följande artikeln om hur du hanterar Data Box via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


