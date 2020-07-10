---
title: Självstudie för att skicka Azure Data Box tillbaka i export ordning | Microsoft Docs
description: Lär dig hur du levererar Azure Data Box till Microsoft när export ordningen är klar
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7023d29bcb559f4edf11b374b9bfb959e968f626
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86210450"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Självstudie: returnera Azure Data Box (för hands version)


I den här självstudien beskrivs hur du returnerar Azure Data Box och data raderas när enheten tas emot på Azure-data.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Radera data från Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

- Du har slutfört [självstudien: kopiera data från Azure Data Box ](data-box-deploy-export-copy-data.md). 
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Nästa steg beror på var du returnerar enheten.

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

Kontrol lera att data kopieringen från enheten är klar och att **Förbered för att skicka** körningen har slutförts. Stegen skiljer sig beroende på i vilken region du skickar enheten.

## <a name="in-us-canada-europe"></a>[I USA, Kanada, Europa](#tab/in-us-canada-europe)

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


## <a name="in-australia"></a>[I Australien](#tab/in-australia)

Azure-datacenter i Australien kräver ytterligare en säkerhetsavisering. För alla inkommande leveranser krävs en särskild avisering. Följ stegen nedan vid leveranser i Australien.


1. Returnera enheten i originalkartongen.
2. Kontrollera att datakopieringen till enheten är klar och att **Förbered för att skicka** lyckades.
3. Stäng av enheten och avlägsna kablarna.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
5. Boka en upphämtning online på [DHL-länken](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="in-japan"></a>[I Japan](#tab/in-japan) 

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


## <a name="in-singapore"></a>[I Singapore](#tab/in-singapore) 

1. Returnera enheten i originalkartongen.
2. Skriv ned spårningsnumret (visas som referensnummer på sidan Förbereda för att skicka i det lokala webbgränssnittet för Data Box). Detta är tillgängligt när steget Förbered för att skicka har slutförts. Hämta frakthandlingen på den här sidan och klistra fast den på förpackningen. 
3. Stäng av enheten och avlägsna kablarna.
4. Rulla ihop strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten. 
5. Skicka ett e-postmeddelande till SingPosts kundtjänst med hjälp av följande e-postmall med spårningsnumret.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > För bokningsbegäranden som tas emot under en arbetsdag:
   > - Före 15:00 sker upphämtningen nästa arbetsdag mellan 09:00 och 13:00.
   > - Efter 15:00 sker upphämtningen nästa arbetsdag mellan 14:00 och 18:00.  


## <a name="self-managed"></a>[Egenhanterad](#tab/in-selfmanaged) 

Om du använder Data Box i Japan, Singapore, Korea och Europa, västra, och har valt alternativet för egenhanterad frakt vid orderskapande, följer du dessa anvisningar. 

1. Skriv ned auktoriseringskoden på sidan Förbereda för att leverera i det lokala webbgränssnittet för Data Box efter att det här steget har slutförts.
2. Stäng av enheten och avlägsna kablarna. Rulla ihop strömkabeln som levererades med enheten och sätt fast kabeln ordentligt på baksidan av enheten.
3. Skicka ett e-postmeddelande till Azure Data Box-driftsteamet med hjälp av mallen nedan när du är redo att returnera enheten.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
---

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När enheten når Azure-datacenter raderar Data Box-enhet data på sina diskar enligt [rikt linjerna i NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Radera data från Data Box

Fortsätt till nästa artikel och lär dig hur du hanterar din Data Box-enhet.

> [!div class="nextstepaction"]
> [Hantera Data Box-enhet via Azure Portal](./data-box-portal-admin.md)


