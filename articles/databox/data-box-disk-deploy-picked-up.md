---
title: Självstudie för att skicka Azure Data Box Disk| Microsoft Docs
description: I den här självstudien förklarar vi hur du skickar tillbaka Azure Data Box-disken till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d0acc2d92c564f89f4777ccb243c7255e47c9105
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514127"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Returnera Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Självstudier: Returnera Azure Data Box Disk 

Den här självstudien beskriver hur du schemalägger en upphämtning för att returnera din Azure Data Box-disk. Instruktionerna för upphämtning beror på var du returnerar enheten. 

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Upphämtning av Data Box Disk i olika regioner

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra följande [självstudie: Kopiera data till en Azure Data Box-disk och verifiera](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Skicka tillbaka Data Box-disken

::: zone-end

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Avgifter kan tillkomma om tillbehören saknas.
    - Återanvänd förpackningen från den första leveransen.  
    - Vi rekommenderar att du förpackar diskarna i ordentligt fäst cellplast.
    - Packa tätt så att den förpackade enheten inte rör sig i kartongen.

Nästa steg beror på var du returnerar enheten. Instruktionerna skiljer sig för USA/Kanada, EU, Australien eller länder i Asien.

### <a name="in-us-or-canadatabin-us-or-canada"></a>[I USA eller Kanada](#tab/in-us-or-canada)

Utför följande steg om du returnerar enheten i USA eller Kanada.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Gå till **Översikt > Ladda ned adressetikett** och ladda ned en returetikett.
    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Schemalägg en upphämtning med UPS. Så här schemalägger du en upphämtning:

    - Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
    - Uppge spårningsnumret för returförsändelsen som visas på den utskrivna etiketten.
    - Om du inte uppger spårningsnumret kräver UPS att du betalar en extra avgift vid upphämtningen.
    - I stället för att schemalägga upphämtningen kan du även lämna in Data Box Disk-enheten på närmaste inlämningsställe.

### <a name="in-europetabin-europe"></a>[I Europa](#tab/in-europe)

Utför följande steg om du returnerar enheten i Europa.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Gå till **Översikt > Ladda ned adressetikett** och ladda ned en returetikett.
    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats.
4. Navigera till DHL-webbplatsen för ditt land/din region. Under Våra divisioner klickar du på DHL Express. Välj **Skicka > Skicka online**.    
3. Identifiera ditt fraktsedelsnummer och klicka på **Boka upphämtning**.

### <a name="in-australiatabin-australia"></a>[I Australien](#tab/in-australia)

Azure-datacenter i Australien kräver ytterligare en säkerhetsavisering. För alla inkommande leveranser krävs en särskild avisering. Följ stegen nedan vid upphämtning i Australien.

1. Använd den tillhandahållna returetiketten och kontrollera att TAU-koden (referensnummer) är skriven på den. Om den tillhandahållna adressetiketten saknas eller om du har något annat problem skickar du ett e-postmeddelande till [Data Box Asia Operations](mailto:adbo@microsoft.com). Ange beställningsnamnet i ämnesrubriken och information om problemet du har.
3. Fäst etiketten på kartongen. 
4. Boka en upphämtning online på länken https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference. 

### <a name="in-japantabin-japan"></a>[I Japan](#tab/in-japan)

1. Skriv företagets namn och adressuppgifter på fraktsedeln som din avsändarinformation.
2. Skicka ett e-postmeddelande till Quantium Solutions baserat på följande e-postmall.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Om du hämtar upp i Osaka** ändrar du ämnet i e-postmallen till: `Pickup request for Microsoft Azure OSA`.
    - Om Chakubarai-fraktsedeln från Japan Post inte inkluderades eller saknas, nämner du det i e-postmeddelandet. Quantium Solutions Japan ber Japan Post att ta med fraktsedeln vid upphämtningen.
    - Om du har flera beställningar som ska hämtas upp separat meddelar du detta via e-post.

3. Se till att du får en e-postbekräftelse från Quantium Solutions när du har bokat en upphämtning. E-postbekräftelsen innehåller även information om Chakubarai-fraktsedeln.

Vid behov kan du kontakta Quantium Solutions support (på japanska) med följande information: 

- E-postadress: Customerservice.JP@quantiumsolutions.com 
- Telefon：03-5755-0150 

### <a name="in-koreatabin-korea"></a>[I Korea](#tab/in-korea)

1. Se till att inkludera returfraktsedeln.
2. Så här begär du upphämtning när returfraktsedeln finns:
    1. Ring journumret för *Quantium Solutions International* på 070-8231-1418 under kontorstider (10–17, måndag till fredag). Ange *Microsoft Azure pickup* och numret för tjänstbegäran för att anordna en upphämtning.  
    2. Om journumret är upptaget skickar du ett e-postmeddelande till `microsoft@rocketparcel.com`, med e-postämnet *Microsoft Azure Pickup* och numret för tjänstbegäran som referens.
    3. Om fraktföretaget inte anländer för upphämtning ringer du journumret för *Quantium Solutions International* för alternativa lösningar. 
    4. Du får en bekräftelse via e-post för upphämtningsschemat.
3. Utför det här steget bara om det inte finns någon fraktsedel. Så här begär du upphämtning:
    1. Ring journumret för *Quantium Solutions International* på 070-8231-1418 under kontorstider (10–17, måndag till fredag). Ange *Microsoft Azure pickup* och numret för tjänstbegäran för att anordna en upphämtning. Ange att du behöver en ny fraktsedel för att anordna en upphämtning. Ange avsändare (kund), information om mottagare (Azure-datacenter) och referensnummer (nummer för tjänstbegäran). 
    2. Om journumret är upptaget skickar du ett e-postmeddelande till `microsoft@rocketparcel.com`, med e-postämnet *Microsoft Azure Pickup* och numret för tjänstbegäran som referens.
    3. Om fraktföretaget inte anländer för upphämtning ringer du journumret för *Quantium Solutions International* för alternativa lösningar. 
    4. Du får en muntlig bekräftelse om begäran görs via telefon.


### <a name="in-singaporetabin-singapore"></a>[I Singapore](#tab/in-singapore)

1. Skriv ut adressetiketten och fäst den på kartongen. Om etiketten är skadad eller förlorad:
    - Gå till **Översikt > Ladda ned adressetikett** och hämta en returetikett.
    - Fäst etiketten på enheten. Se till att etiketten syns tydligt.

2. För att begära upphämtning skickar du e-post till SingPost-kundtjänsten med följande mall och spårningsnumret (spårningsnumret kan hittas på returetiketten som tillhandahålls i det levererade paketet).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - XZ00001234567 
    Body: 
     a. Requestor name
     b. Requestor contact number
     c. Requestor collection address
     d. Preferred collection date
    ```

   > [!NOTE]
   > För bokningsbegäranden som tas emot under en arbetsdag:
   > - Före 15:00 sker upphämtningen nästa arbetsdag mellan 09:00 och 13:00.
   > - Efter 15:00 sker upphämtningen nästa arbetsdag mellan 14:00 och 18:00.

   Om du stöter på några problem kan du kontakta Data Box Operations Asia på adbo@microsoft.com. I ämnesrubriken anger du jobbnamnet och det problem som påträffats.

3. Lämna över till fraktföretaget. 


::: zone target="docs"

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Upphämtning av Data Box Disk i olika regioner

Gå vidare till nästa anvisningar för att lära dig hur du verifierar datauppladdning från Data Box Disk till Azure Storage-kontot.

> [!div class="nextstepaction"]
> [Verifiera datauppladdning från Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end




