---
title: Självstudie för att leverera Azure Data Box Disk | Microsoft Docs
description: I den här självstudien förklarar vi hur du skickar tillbaka Azure Data Box-disken till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 9e3159a083d21e4a7a81df2fcf7a7d5db882b757
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976916"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box-disken och verifiera datauppladdning till Azure

Detta är den sista självstudien i serien: Distribuera Azure Data Box Disk. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Skicka Data Box-disk till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box-disk

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra följande [självstudie: Kopiera data till en Azure Data Box-disk och verifiera](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Skicka tillbaka Data Box-disken

1. När dataverifieringen har genomförts kan du koppla från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Avgifter kan tillkomma om tillbehören saknas.
    - Återanvänd förpackningen från den första leveransen.  
    - Vi rekommenderar att du packar diskar med en välsäkrad omslutning.
    - Se till att anpassningen är Snug för att minska eventuella rörelser i rutan.

Nästa steg bestäms av var du returnerar enheten.

### <a name="pick-up-in-us-canada"></a>Hämta i USA, Kanada

Utför följande steg om du returnerar enheten i USA eller Kanada.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Öppna **Översikt > Ladda ned adressetikett**.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Den här åtgärden laddar ned en adressetikett. Se nedan.

        ![Exempel på fraktsedel](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Schemalägg hämtning med UPS. Så här schemalägger du en upphämtning:

    - Ring den lokala UPS-enheten (lands-/regionsspecifika avgiftsfritt nummer).
    - I ditt samtal offerterar du det omvända skeppnings numret som det visas i den utskrivna etiketten.
    - Om spårnings numret inte anges kräver UPS att du betalar ytterligare en kostnad under upphämtningen.
    - I stället för att schemalägga upphämtningen kan du också ta bort Data Box Disk på den närmaste List rutan.


### <a name="pick-up-in-europe"></a>Hämta i Europa

Utför följande steg om du returnerar enheten i Europa.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller förlorad:
    - Öppna **Översikt > Ladda ned adressetikett**.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Den här åtgärden laddar ned en adressetikett. Se nedan.

        ![Exempel på fraktsedel](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Fäst etiketten på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats.
4. Gå till webbplatsen för land/region DHL Express och välj **bok en courier-samling > eReturn-försändelse**.

    ![DHL-returutleverans](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Identifiera ditt fraktsedelsnummer och klicka på **Boka upphämtning**.

      ![Schemalägg hämtning](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Hämta i Asien och Stilla havs området

Den här regionen innehåller instruktioner för hämtning i Japan, Korea, Australien och Singapore.

#### <a name="pick-up-in-australia"></a>Hämta i Australien

Azure-datacenter i Australien har ytterligare en säkerhets avisering. Alla inkommande leveranser måste ha en avancerad avisering. Utför följande steg för att hämta i Australien.

1. E `adbops@microsoft.com` -postadress för att begära leverans etikett med unikt inkommande ID eller Tau-koden. Placera begäran minst tre dagar före det planerade leverans datumet för att hämta etiketten.
2. E-postmeddelandets ämne bör vara *begäran om omvänt frakt sedel med tau-kod*. Se till att ta med följande information i e-postmeddelandet: 

    - Beställningens namn
    - Adress
    - Kontaktnamn

#### <a name="pick-up-in-japan"></a>Hämta i Japan

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

#### <a name="pick-up-in-korea"></a>Hämta i Korea

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
    - Öppna **Översikt > Ladda ned adressetikett**.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Den här åtgärden laddar ned en adressetikett. Se nedan.

        ![Exempel på fraktsedel](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Fäst etiketten på enheten. Kontrol lera att etiketten är synlig.

2. Så här begär du hämtning:
    - Ring **SingPost** hotline vid **6845 6485** under kontors tid (9 till 17, måndag till fredag).  
    - Offert *Microsoft Azure upphämtning* och service förfrågnings nummer (spårnings nummer i etiketten retur leverans) för att ordna för en samling. 
    - Du får en muntlig bekräftelse av upphämtnings schema. 
    - Om courier inte tar emot insamlingen anropar du **SingPost** på **6845 6485** för alternativa arrangemang. 
3. Handen till courier. 


## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När diskarna hämtas upp ändras statusen i portalen till **upphämtad**. Du får också ett spårnings-ID.

![Upphämtade diskar](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

När Microsoft tar emot och genomsöker disken uppdateras statusen till **mottagen**. 

![Mottagna diskar](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

När diskarna ansluts till en server i Azure-datacentret kopieras data automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

![Datakopiering slutförd](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Om kopieringen är klar med fel, se [Felsöka uppladdnings fel](data-box-disk-troubleshoot-upload.md).

Kontrollera att dina data finns på lagringskontot innan du tar bort dem från källan. Dina data kan vara i:

- Ditt Azure Storage konto (er). När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- En eller flera av de hanterade disk resurs grupperna. När du skapar hanterade diskar laddas de virtuella hård diskarna som Page blobbar och konverteras sedan till hanterade diskar. De hanterade diskarna är anslutna till de resurs grupper som anges vid tidpunkten för skapande av order.

  - Om din kopia till hanterade diskar i Azure lyckades, kan du gå till **order informationen** i Azure Portal och anteckna resurs gruppen som angetts för hanterade diskar.

      ![Visa beställnings information](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Gå till den angivna resurs gruppen och leta upp dina hanterade diskar.

      ![Resurs grupp för hanterade diskar](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Om du har kopierat en VHDX eller en dynamisk/differentierad virtuell hård disk överförs VHDX/VHD till mellanlagrings kontot som en Block-Blob. Gå till ditt **lagrings konto > blobbar** och välj sedan lämplig container-StandardSSD, StandardHDD eller PremiumSSD. VHDX/VHD: er bör visas som block blobbar i ditt lagrings konto för lagring.

Så här kontrollerar du att data har överförts till Azure:

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

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Returnera disken och verifiera uppladdning till Azure

## <a name="ship-to-azure"></a>Skicka till Azure

1. När data verifieringen är klar kopplar du från diskarna. Ta bort anslutningskablarna.
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Avgifter kan tillkomma om tillbehören saknas.
    - Återanvänd förpackningen från den första leveransen.  
    - Vi rekommenderar att du packar diskar med en välsäkrad omslutning.
    - Se till att anpassningen är Snug för att minska eventuella rörelser i rutan.
3. Nästa steg bestäms av var du returnerar enheten.
    - [Schemalägg en upphämtning med UPS om du returnerar enheten i USA och Kanada](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Schemalägg hämtning med DHL för Europa](data-box-disk-deploy-picked-up.md#pick-up-in-europe) genom att besöka deras webbplats och ange flyg faktura nummer.
    - [Schemalägg hämtning för länder i Australien – Stilla havs området](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region) , till exempel Australien, Japan, Korea och Singapore.
4. När diskarna har hämtats av operatören, uppdateras order statusen i portalen och ett spårnings-ID visas.

## <a name="verify-upload-to-azure"></a>Verifiera uppladdning till Azure

När data har överförts till Azure kontrollerar du att dina data finns på lagrings kontona innan du tar bort dem från källan. Dina data kan vara i:

- Ditt Azure Storage konto (er). När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

    - **För block-blobbar och Page blobbar**: https://< storage_account_name >. blob. Core. Windows. net/<containername>/Files/a.txt

    - **För Azure Files**: https://< storage_account_name >. File. Core. Windows. net/<sharename>/Files/a.txt

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- En eller flera av de hanterade disk resurs grupperna. När du skapar hanterade diskar laddas de virtuella hård diskarna som Page blobbar och konverteras sedan till hanterade diskar. De hanterade diskarna är anslutna till de resurs grupper som anges vid tidpunkten för skapande av order.

::: zone-end


