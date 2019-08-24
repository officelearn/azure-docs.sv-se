---
title: Självstudie för att leverera Azure Data Box Disk | Microsoft Docs
description: I den här självstudien förklarar vi hur du skickar tillbaka Azure Data Box-disken till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6dcd55cc2c269024179e450d789df544bb8ff9a9
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012923"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Självstudier: Returnera Azure Data Box Disk 

I den här självstudien beskrivs hur du schemalägger en hämtning för att returnera din Azure Data Box Disk. Hämtnings anvisningarna beror på var du returnerar enheten. 

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Hämta Data Box Disk i olika regioner


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra följande [självstudie: Kopiera data till en Azure Data Box-disk och verifiera](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Skicka tillbaka Data Box-disken

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Avgifter kan tillkomma om tillbehören saknas.
    - Återanvänd förpackningen från den första leveransen.  
    - Vi rekommenderar att du packar diskar med en välsäkrad omslutning.
    - Se till att anpassningen är Snug för att minska eventuella rörelser i rutan.

Nästa steg bestäms av var du returnerar enheten.

## <a name="pick-up-in-us-canada"></a>Hämta i USA, Kanada

Utför följande steg om du returnerar enheten i USA eller Kanada.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Gå till **översikt > Ladda ned frakt etikett** och ladda ned en retur leverans etikett.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Schemalägg hämtning med UPS. Så här schemalägger du en upphämtning:

    - Ring den lokala UPS-enheten (lands-/regionsspecifika avgiftsfritt nummer).
    - I ditt samtal offerterar du det omvända skeppnings numret som det visas i den utskrivna etiketten.
    - Om spårnings numret inte anges kräver UPS att du betalar ytterligare en kostnad under upphämtningen.
    - I stället för att schemalägga upphämtningen kan du också ta bort Data Box Disk på den närmaste List rutan.


## <a name="pick-up-in-europe"></a>Hämta i Europa

Utför följande steg om du returnerar enheten i Europa.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Gå till **översikt > Ladda ned frakt etikett** och ladda ned en retur leverans etikett.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats.
4. Gå till webbplatsen för land/region DHL Express och välj **bok en courier-samling > eReturn-försändelse**.

    ![DHL-returutleverans](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Identifiera ditt fraktsedelsnummer och klicka på **Boka upphämtning**.

      ![Schemalägg hämtning](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

## <a name="pick-up-in-asia-pacific"></a>Hämta i Asien – Stilla havs området

Den här regionen innehåller instruktioner för hämtning i Japan, Korea, Australien och Singapore.

### <a name="pick-up-in-australia"></a>Hämta i Australien

Azure-datacenter i Australien har ytterligare en säkerhets avisering. Alla inkommande leveranser måste ha en avancerad avisering. Utför följande steg för att hämta i Australien.

1. E `adbops@microsoft.com` -postadress för att begära leverans etikett med unikt inkommande ID eller Tau-koden. Placera begäran minst tre dagar före det planerade leverans datumet för att hämta etiketten.
2. E-postmeddelandets ämne bör vara *begäran om omvänt frakt sedel med tau-kod*. Se till att ta med följande information i e-postmeddelandet: 

    - Beställningens namn
    - Adress
    - Kontaktnamn

### <a name="pick-up-in-japan"></a>Hämta i Japan

1. Skriv företagets namn och adress information på frakt sedeln som avsändar information.
2. Quantium-lösning för e-post med hjälp av följande e-postmall.

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
    - **Om du**väljer att hämta i Osaka ändrar du ämnet i e-postmallen till: `Pickup request for Microsoft Azure OSA`.
    - Om Japan post Chakubarai frakt NOTE inte ingår eller saknas, Observera i det här e-postmeddelandet. Quantium-lösningar i Japan kommer att begära en japansk post för att ta frakt sedeln vid upphämtning.
    - Om du har flera order, e-post för att se till att enskilda upphämtningar.

3. Få en bekräftelse via e-post från Quantium-lösningar när du har bokat en hämtning. Bekräftelse av e-post innehåller också information om Chakubarai frakt Note.

Vid behov kan du kontakta Quantium Solution support (japanska språk) med följande information: 

- ÅtkomstCustomerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

### <a name="pick-up-in-korea"></a>Hämta i Korea

1. Se till att inkludera retur frakt sedeln.
2. Så här begär du hämtning av frakt sedel:
    1. Ring *Quantium Solutions International* hotline vid 070-8231-1418 under kontors tid (10-till-5 PM, måndag till fredag). Offert *Microsoft Azure upphämtning* och service förfrågnings numret för att ordna för en samling.  
    2. Om Hotline är upptagen, e- `microsoft@rocketparcel.com`post med e-postmeddelandets ämnesrad *Microsoft Azure* upphämtningen och service förfrågnings numret som referens.
    3. Om courier inte tar emot insamling kan du anropa *Quantium Solutions International* Hotline för alternativa arrangemang. 
    4. Du får en bekräftelse via e-post för upphämtnings schema.
3. Utför bara det här steget om frakt sedeln inte finns. Så här begär du hämtning:
    1. Ring *Quantium Solutions International* hotline vid 070-8231-1418 under kontors tid (10-till-5 PM, måndag till fredag). Offert *Microsoft Azure upphämtning* och service förfrågnings numret för att ordna för en samling. Ange att du behöver en ny frakt anteckning för att ordna för en samling. Ange avsändare (kund), mottagar information (Azure-datacenter) och referens nummer (service förfrågnings nummer). 
    2. Om Hotline är upptagen, e- `microsoft@rocketparcel.com`post med e-postmeddelandets ämnesrad *Microsoft Azure* upphämtningen och service förfrågnings numret som referens.
    3. Om courier inte tar emot insamling kan du anropa *Quantium Solutions International* Hotline för alternativa arrangemang. 
    4. Du får en muntlig bekräftelse om begäran görs via telefon.

### <a name="pick-up-in-singapore"></a>Hämta i Singapore

1. Skriv ut etiketten frakt och bifoga till rutan. Om etiketten är skadad eller förlorad:
    - Gå till **översikt > Ladda ned frakt etikett** och hämta en etikett för retur leverans.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - Fäst etiketten på enheten. Kontrol lera att etiketten är synlig.

2. Så här begär du hämtning:
    - Ring **SingPost** hotline vid **6845 6485** under kontors tid (9 till 17, måndag till fredag).  
    - Offert *Microsoft Azure upphämtning* och service förfrågnings nummer (spårnings nummer i etiketten retur leverans) för att ordna för en samling. 
    - Du får en muntlig bekräftelse av upphämtnings schema. 
    - Om courier inte tar emot insamlingen anropar du **SingPost** på **6845 6485** för alternativa arrangemang. 
3. Handen till courier. 


## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Hämta Data Box Disk i olika regioner

Gå vidare till nästa anvisningar för att lära dig hur du verifierar data uppladdning från Data Box Disk till Azure Storage-kontot.

> [!div class="nextstepaction"]
> [Verifiera data uppladdning från Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-to-azure"></a>Returnera disken till Azure

1. När data verifieringen är klar kopplar du från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Avgifter kan tillkomma om tillbehören saknas.
    - Återanvänd förpackningen från den första leveransen.  
    - Vi rekommenderar att du packar diskar med en välsäkrad omslutning.
    - Se till att anpassningen är Snug för att minska eventuella rörelser i rutan.

3. Nästa steg bestäms av var du returnerar enheten.

    - [Schemalägg en upphämtning med UPS om du returnerar enheten i USA och Kanada](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Schemalägg hämtning med DHL för Europa](data-box-disk-deploy-picked-up.md#pick-up-in-europe) genom att besöka deras webbplats och ange flyg faktura nummer.
    - [Schemalägg hämtning för länder i Australien – Stilla havs området](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific) , till exempel Australien, Japan, Korea och Singapore.

När diskarna har hämtats av operatören, uppdateras order statusen i portalen och ett spårnings-ID visas.

::: zone-end


