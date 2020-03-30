---
title: Privata SKU:er och planer | Azure Marketplace
description: Så här använder du privata SKU:er för att hantera erbjudandets tillgänglighet.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280396"
---
<a name="private-skus-and-plans"></a>Privata SKU:er och planer
============

Med privata SKU:er kan du begränsa tillgängligheten för SKU:er till specifika kunder. När en SKU är markerad privat är den inte tillgänglig i någon offentlig katalog, inklusive på [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](https://portal.azure.com). På Azure-portalen kan endast kunder med åtkomst till SKU se den. Dessutom skulle de också uppmanas att de har tillgång till privata erbjudanden.

>[!NOTE]
>Privata SKU:er måste ha nya unika SKU/Plan-ID:n för att undvika konflikter med dina offentliga SKU:er.

Du kan använda privata SKU:er för att hantera följande scenarier:

1.  Publicera programvara som du bara vill vara tillgänglig för allmänheten för specifika kunder och inte tillgänglig för allmänheten.
2.  Publicera varianter av offentlig programvara till ett anpassat pris för specifika kunder.
3.  Publicera varianter av offentlig programvara med en anpassad beskrivning och villkor (via nytt erbjudande).

Om du bara vill ändra priset kan du återanvända diskarna från en annan SKU i samma erbjudande. Med privata SKU:er behöver du inte skicka in diskar på nytt över SKU:er.

<a name="mark-a-sku-private"></a>Markera en privat SKU
---------------------

Om du vill markera en SKU som privat växlar du alternativet och frågar om SKU:et är privat:

![Markera en SKU som privat](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Du kan återanvända diskarna i en annan SKU och ändra prissättningen eller beskrivningen. Om du vill återanvända diskarna väljer du **Ja** som svar på uppmaningen "Har den här SKU-återanvändningen av bilder från en offentlig SKU".

Om SKU:n är markerad som privat och erbjudandet har andra SKU:er med återanvändbara diskar måste du ange att SKU återanvänder diskar från en annan SKU. Du måste också ange målgruppen för den privata SKU:n.

>[!NOTE]
>När den har publicerats kan en offentlig SKU inte göras privat.

<a name="select-an-image"></a>Markera en bild
------------------

Du kan ange nya diskar för den privata SKU eller återanvända samma diskar som redan finns i en annan SKU, bara ändra prissättning eller beskrivning. Om du vill återanvända diskarna väljer du **Ja** som svar på uppmaningen "Har den här SKU-återanvändningen från en offentlig SKU"-prompt.

![Ange återanvändning av bilder](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

När du har bekräftat att SKU återanvänder *base* bilder väljer du käll- eller bas-SKU för bilderna:

![Markera en bild](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

När du publicerar erbjudandet görs bilderna från den valda SKU tillgängliga under det privata SKU-ID:t med anpassade priser/villkor. Den privata SKU skulle bara vara synlig för målgruppen.

För avbildningsuppdateringar måste du bara uppdatera den underliggande SKU:s avbildning. Bakom kulisserna uppdateras även bilden för den privata SKU:n automatiskt. Om du tar bort bilden från den underliggande SKU:n tas bilden också bort från den privata SKU:n.

<a name="restricting-the-audience"></a>Begränsa målgruppen
------------------------

Privata erbjudanden kan endast hittas och distribueras av riktade användare.
För närvarande stöder vi inriktning på användare med hjälp av prenumerations-ID.

Dessa prenumerationer kan anges via ett manuellt **anmälningsformulär för upp till 10 prenumerationer**, eller genom att ladda upp en CSV-fil, vilket möjliggör upp till **20.000 prenumerationer**.

Manuell post för begränsad publik:

![Begränsa målgruppen manuellt](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV-uppladdning för begränsad målgrupp:

![Använda CSV för att begränsa målgruppen](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Exempel på CSV-filinnehåll:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

När du växlar från manuell inmatning till CSV-uppladdningsvy eller från CSV till manuell post behålls inte den gamla listan med prenumerations-ID med åtkomst till SKU. En varning visas och listan skrivs bara över när erbjudandet sparas.

<a name="managing-private-audiences"></a>Hantera privata målgrupper
-------------------------

**För att uppdatera målgruppen utan att publicera hela erbjudandet på nytt gör du de målgruppsändringar du vill ha (med antingen användargränssnittet eller API:et) och startar sedan åtgärden Synkronisera privata målgrupper.**

Om din målgrupp är 10 eller färre prenumerationer kan du hantera den helt med hjälp av CPP-användargränssnittet.

Om din målgrupp är fler än 10 prenumerationer kan du hantera den med en CSV-fil som du antingen kan ladda upp till CPP-användargränssnittet eller använda API:et.

Om du använder API:et och inte vill underhålla en CSV-fil kan du hantera målgruppen direkt med API enligt instruktionerna nedan.

> [!NOTE]
> Använd Azure-prenumerations-ID (Abonnemang och SKU: er) eller endast klient-ID (Endast abonnemang) för att lägga till en målgrupp i ditt privata erbjudande.

###  <a name="managing-subscriptions-with-the-api"></a>Hantera prenumerationer med API:et

Du kan använda API:et för att antingen ladda upp en CSV eller hantera din målgrupp direkt (utan att använda en CSV). I allmänhet behöver du bara hämta ditt `restrictedAudience` erbjudande, uppdatera objektet och sedan skicka tillbaka ändringarna till erbjudandet för att lägga till eller ta bort målgruppsmedlemmar.

Så här uppdaterar du din målgruppslista på ett programmatiskt sätt:

1. [Hämta dina erbjudandedata:](cloud-partner-portal-api-retrieve-specific-offer.md)

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Hitta begränsade målgruppsobjekt i varje SKU i erbjudandet med den här JPath-frågan:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Uppdatera de begränsade målgruppsobjekten för ditt erbjudande.

    **Om du ursprungligen laddade upp prenumerationslistan för ditt privata erbjudande från CSV-filen:**

    Dina *restrictedAudience-objekt* kommer att se ut så här.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    För varje begränsat målgruppsobjekt:

    a. Ladda ner `restrictedAudience.uploadedCsvUri`innehållet i . Innehållet är helt enkelt en CSV-fil med rubriker. Ett exempel:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Lägg till eller ta bort prenumerationer i den nedladdade CSV-filen efter behov.

    c. Ladda upp den uppdaterade CSV-filen till en plats, till exempel [Azure Blob](../../storage/blobs/storage-blobs-overview.md) storage eller [OneDrive](https://onedrive.live.com), och skapa en skrivskyddad länk till filen. Detta kommer att bli din nya *SasUrl*.

    d. Uppdatera `restrictedAudience.uploadedCsvUri` nyckeln med din nya *SasUrl*.

    **Om du manuellt angav den ursprungliga listan över prenumerationer för ditt privata erbjudande från Cloud Partner Portal:**

    Ditt *restrictedAudience-objekt* kommer att se ut ungefär så här:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. För varje begränsat målgruppsobjekt lägger `restrictedAudience.manualEntries` du till eller tar bort poster i listan efter behov.

4. När du är klar med uppdateringen av alla *restrictedAudience-objekt* för varje SKU i ditt privata erbjudande [uppdaterar du erbjudandet:](cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Med det är din uppdaterade målgruppslista nu i kraft.

<a name="previewing-private-offers"></a>Förhandsgranska privata erbjudanden
-------------------------

Under förhandsgransknings-/mellanlagringssteget kan endast förhandsprenumerationer på erbjudandenivå komma åt SKU.During the preview/staging step, only the offer level preview subscriptions will be able to access the SKU. I det här testskedet kan du förhandsgranska erbjudandet som det verkar för dina målkunder.

Förhandsprenumerationer på erbjudandenivå för att komma åt stegvisa erbjudanden:

![Förhandsgranska prenumerations-ID:er](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

När erbjudandet har live visas endast de begränsade målgruppsprenumerationerna (som anges via manuell post eller CSV) för att visa och distribuera den privata SKU:n. Vi rekommenderar att du **alltid inkluderar dina egna prenumerationer i den begränsade målgruppen** för den privata SKU för valideringsändamål.

>[!NOTE]
>I felsökningssyfte har Microsofts support- och teknikteam också tillgång till dessa privata erbjudanden.
