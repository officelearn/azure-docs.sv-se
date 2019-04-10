---
title: Skicka tillbaka Microsoft Azure Data Box-disk| Microsoft Docs
description: I den här självstudien förklarar vi hur du skickar tillbaka Azure Data Box-disken till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7d1d38a2e8d3216414ab215f3023d4a188c57120
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282907"
---
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
2. Slå in alla diskar och anslutningskablarna i bubbelplast och lägg dem i fraktlådan. Kostnader kan tillkomma om tillbehör saknas.
    - Återanvända paketering från den första transporten.  
    - Vi rekommenderar att du packar diskar med väl skyddade bubbled över.
    - Kontrollera att passar är ordentligt att minska eventuella förflyttningar i rutan.

Nästa steg bestäms av där du returnerar enheten.

### <a name="pick-up-in-us-canada"></a>Hämta i USA, Kanada

Gör följande om Returnerar enheten i USA eller Kanada.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller tappas bort:
    - Öppna **Översikt > Ladda ned adressetikett**.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Den här åtgärden laddar ned en adressetikett. Se nedan.

        ![Exempel på fraktsedel](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Fästa etikett på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Schemalägga en upphämtning med Avbrottsfria.


### <a name="pick-up-in-europe"></a>Hämta i Europa

Gör följande om enheten och returnerar Europa.

1. Använd returetiketten i den genomskinliga plastficka som sitter på lådan. Om etiketten är skadad eller tappas bort:
    - Öppna **Översikt > Ladda ned adressetikett**.

        ![Ladda ned adressetikett](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Den här åtgärden laddar ned en adressetikett. Se nedan.

        ![Exempel på fraktsedel](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Fästa etikett på enheten.

2. Försegla fraktlådan och se till att adressetiketten är väl synlig.
3. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats.
4. Navigera till DHL-webbplatsen för ditt land. Under Våra divisioner klickar du på DHL Express. Välj **Skicka > Skicka online**.

    ![DHL returleverans](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Identifiera ditt fraktsedelsnummer och klicka på **Boka upphämtning**.

      ![Boka upphämtning](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Hämta i Asien / Stillahavsområdet

Den här regionen innehåller anvisningar för hämtning i Japan, Sydkorea och Australien.

#### <a name="pick-up-in-australia"></a>Hämta i Australien

Azure-datacenter i Australien har ett meddelande om ytterligare säkerhet. Alla inkommande leveranser måste ha en meddelanden i förväg. Gör följande för hämtning i Australien.

1. E-post `adbops@microsoft.com` till begäran leverans etikett med unikt ID för inkommande eller TAU-kod. Placera minst 3 dagar före det planerade leverera datumet att hämta etiketten i tid för begäran.
2. E-postämnet ska vara - *förfrågan om omvänd adressetikett med TAU kod*. Se till att inkludera följande information i e-postmeddelandet: 

    - Beställningens namn
    - Adress
    - Kontaktnamn

#### <a name="pick-up-in-japan"></a>Hämta i Japan

1. Se till att inkludera returnerade connote av Japan Post Chakubarai.
2. Skriv ditt företag namn och adress information på connote som denna information.
3. Ring Japan Post upphämtningsförfrågan nummer 0800 0800 111 (kostnadsfri begäran). Ring 7 siffror postnummer för upphämtning adress och sedan vidarebefordra till närmaste post kontoret.
    - Tillgängliga tider för förfrågan om upphämtning beror på respektive post kontor.
    - Informera att leveransen finns för Japan Post Chakubarai Yu-paket.
    - Använd Chakubarai betyder som har inkluderats.
4. Om Japan Post Chakubarai betyder var inte ingår, e- *Quantium lösningar* på `Customerservice.JP@quantiumsolutions.com`. *Quantium lösningar* begär Japan inlägg att hämta och be dem att ta med connote vid hämtning.
    - Ange referens nummer på Chakubarai betyder kommentar kolumnen som Japan Post kommer att få.
    - Ange leveransadressen som visas nedan:   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-4-3 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Om Chakubarai betyder saknas, kan du begära hämtning via e-post. Du kan använda följande e-postmallen för att begära hämtning.

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>Hämta i Sydkorea

1. Se till att inkludera returnerade connote.
2. Att begära hämtning:
    1. Anropa *Quantium lösningar International* ditt lokala 070 8231 1418 under kontorstid (10 AM till 17: 00, måndag till fredag). Citera *Microsoft upphämtning* och connote talet att ordna för en samling.  
    2. Om i ditt lokala är upptagen, e- `microsoft@rocketparcel.com`, med e-postämnet *Microsoft Pickup* och connote många som referens.
    3. Om bud inte har kommit för samlingen, anropa *Quantium lösningar International* ditt lokala alternativa regler. 

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När diskarna hämtas upp ändras statusen i portalen till **upphämtad**. Du får också ett spårnings-ID.

![Upphämtade diskar](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

När Microsoft tar emot och genomsöker disken uppdateras statusen till **mottagen**. 

![Mottagna diskar](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

När diskarna ansluts till en server i Azure-datacentret kopieras data automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

![Datakopiering slutförd](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. Dina data kan ha:

- Dina Azure Storage-konton. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

  - För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

- Din hanterade disk resursgrupperna. När du skapar hanterade diskar kan de virtuella hårddiskarna överförs som sidblobar och konverteras sedan till hanterade diskar. De hanterade diskarna är kopplade till resursgrupper som anges vid tidpunkten för skapande av.

  - Om din kopia till managed disks i Azure går du till den **Order information** i Azure-portalen och gör en anteckning på resursgruppen som angetts för hanterade diskar.

      ![Visa beställningsinformation](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Gå till antecknat resursgruppen och leta upp din hanterade diskar.

      ![Resursgrupp för hanterade diskar](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Om du har kopierat en vhdx-disk eller en dynamisk/differentierande virtuell Hårddisk har i VHDX/VHD överförts till mellanlagringskontot som en blockblob. Gå till din mellanlagring **lagringskonto > Blobar** och välj sedan så att rätt behållare - StandardSSD, StandardHDD eller PremiumSSD. De VHDX/virtuella hårddiskarna ska visas som blockblobar i din mellanlagringskontot.

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
> [Använda Azure-portalen för att administrera Azure Data Box-Disk](./data-box-portal-ui-admin.md)


