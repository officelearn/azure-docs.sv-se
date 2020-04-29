---
title: 'Privata SKU: er och planer | Azure Marketplace'
description: 'Så här använder du privata SKU: er för att hantera Erbjudandets tillgänglighet.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280396"
---
<a name="private-skus-and-plans"></a>Privata SKU: er och planer
============

Med privata SKU: er kan du begränsa tillgängligheten för SKU: er till vissa kunder. När en SKU har marker ATS som privat är den inte tillgänglig i någon offentlig katalog, inklusive på [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure Portal](https://portal.azure.com). På den Azure Portal kan endast kunder med åtkomst till SKU: n se det. Dessutom uppmanas de också att få åtkomst till privata erbjudanden.

>[!NOTE]
>Privata SKU: er måste ha nya unika SKU/plan-ID: n för att undvika konflikter med dina offentliga SKU: er.

Du kan använda privata SKU: er för att hantera följande scenarier:

1.  Publicera program vara som du bara vill ska vara tillgänglig offentligt för vissa kunder och inte offentligt tillgänglig.
2.  Publicera varianter av offentlig program vara till ett anpassat pris för vissa kunder.
3.  Publicera varianter av offentlig program vara med en anpassad beskrivning och villkor (via nytt erbjudande).

Om du bara vill ändra priset kan du återanvända diskarna från en annan SKU i samma erbjudande. Med privata SKU: er behöver du inte skicka om diskar över SKU: er.

<a name="mark-a-sku-private"></a>Markera en SKU privat
---------------------

Om du vill markera en SKU som privat, kan du växla alternativet som frågar om SKU: n är privat:

![Markera en SKU som privat](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Du kan återanvända diskarna i en annan SKU och ändra prissättningen eller beskrivningen. Om du vill återanvända diskarna väljer du **Ja** som svar på "gör denna SKU Återanvänd avbildningar från en offentlig SKU".

Om SKU: n har marker ATS som privat och erbjudandet har andra SKU: er med återanvända diskar, måste du ange att SKU: er återanvänder diskar från en annan SKU. Du måste också ange mål gruppen för den privata SKU: n.

>[!NOTE]
>När den har publicerats kan en offentlig SKU inte göras privat.

<a name="select-an-image"></a>Välj en bild
------------------

Du kan tillhandahålla nya diskar för den privata SKU: n eller återanvända samma diskar som redan finns i en annan SKU, bara ändra prissättningen eller beskrivningen. Om du vill återanvända diskarna väljer du **Ja** som svar på den här SKU: en åter användnings bild från en offentlig SKU-prompt.

![Visa åter användning av bild](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

När du har bekräftat att SKU: er återanvänder avbildningar väljer du källan eller *bas* -SKU: n för avbildningarna:

![Välj en bild](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

När du publicerar erbjudandet görs avbildningarna från den valda SKU: n tillgängliga under det privata SKU-ID: t med anpassade priser/villkor. Det privata SKU: n skulle bara vara synligt för mål gruppen.

För avbildnings uppdateringar behöver du bara uppdatera den underliggande SKU-avbildningen. Bilden för den privata SKU: n kommer också att uppdateras automatiskt i bakgrunden. Om du tar bort avbildningen från den underliggande SKU: n, tas även avbildningen bort från det privata SKU: n.

<a name="restricting-the-audience"></a>Begränsa mål gruppen
------------------------

Privata erbjudanden kan bara hittas och distribueras av riktade användare.
För närvarande stöder vi mål användare som använder prenumerations-ID: n.

Dessa prenumerationer kan anges via ett manuellt registrerings formulär **för upp till 10 prenumerationer**eller genom att ladda upp en CSV-fil som tillåter **upp till 20 000 prenumerationer**.

Manuell inmatning för begränsad mål grupp:

![Begränsa mål gruppen manuellt](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

KLUSTERDELAD överföring för begränsad mål grupp:

![Använd CSV för att begränsa mål gruppen](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Exempel på CSV-fil innehåll:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

När du växlar från manuell inmatning till vyn CSV-överföring eller från CSV till manuell inmatning, behålls inte den gamla listan över prenumerations-ID: n med åtkomst till SKU: n. En varning visas och listan skrivs bara över när du sparar erbjudandet.

<a name="managing-private-audiences"></a>Hantera privata mål grupper
-------------------------

**För att kunna uppdatera mål gruppen utan att publicera om hela erbjudandet gör du önskade ändringar i mål gruppen (antingen användar gränssnittet eller API: et) och startar sedan åtgärden synkronisera privata mål grupper.**

Om din mål grupp är 10 eller färre prenumerationer kan du hantera den helt med hjälp av användar gränssnittet i CPP.

Om mål gruppen är fler än 10 prenumerationer kan du hantera den med hjälp av en CSV-fil som du antingen kan överföra till CPP-ANVÄNDARGRÄNSSNITTET eller använda API: et.

Om du använder API: et och inte vill underhålla en CSV-fil kan du hantera mål gruppen direkt med API enligt anvisningarna nedan.

> [!NOTE]
> Använd Azures prenumerations-ID (planer och SKU: er) eller klient-ID (endast planer) för att lägga till en mål grupp i ditt privata erbjudande.

###  <a name="managing-subscriptions-with-the-api"></a>Hantera prenumerationer med API: et

Du kan använda API: t för att ladda upp en CSV-fil eller hantera din mål grupp direkt (utan att använda en CSV-fil). I allmänhet behöver du bara hämta ditt erbjudande, uppdatera `restrictedAudience` objektet och sedan skicka tillbaka ändringarna till ditt erbjudande för att lägga till eller ta bort mål grupps medlemmar.

Så här uppdaterar du din mål grupps lista program mässigt:

1. [Hämta dina erbjudande](cloud-partner-portal-api-retrieve-specific-offer.md) data:

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Hitta begränsade mål grupps objekt i varje SKU för erbjudandet med denna JPath-fråga:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Uppdatera de begränsade Audience-objekten för ditt erbjudande.

    **Om du ursprungligen laddade upp prenumerations listan för ditt privata erbjudande från CSV-filen:**

    Dina *restrictedAudience* -objekt kommer att se ut så här.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    För varje begränsat mål grupps objekt:

    a. Ladda ned innehållet i `restrictedAudience.uploadedCsvUri`. Innehållet är bara en CSV-fil med huvuden. Ett exempel:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Lägg till eller ta bort prenumerationer i den hämtade CSV-filen efter behov.

    c. Ladda upp den uppdaterade CSV-filen till en plats, till exempel [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) eller [OneDrive](https://onedrive.live.com), och skapa en skrivskyddad länk till filen. Det här är din nya *SasUrl*.

    d. Uppdatera `restrictedAudience.uploadedCsvUri` nyckeln med din nya *SasUrl*.

    **Om du har angett den ursprungliga listan med prenumerationer manuellt för ditt privata erbjudande från Cloud Partner Portal:**

    Dina *restrictedAudience* -objekt ser ut ungefär så här:

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

    a. Lägg till eller ta bort poster i `restrictedAudience.manualEntries` listan efter behov för varje begränsat mål grupps objekt.

4. När du har uppdaterat alla *restrictedAudience* -objekt för varje SKU för ditt privata erbjudande [uppdaterar du erbjudandet](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Med detta gäller nu den uppdaterade mål grupps listan.

<a name="previewing-private-offers"></a>För hands Visa privata erbjudanden
-------------------------

Under för hands versionen/mellanlagringen kommer endast prenumerationerna för för hands versionen av erbjudande nivån att kunna komma åt SKU: n. I det här test skedet kan du förhandsgranska erbjudandet som det skulle visas för dina mål kunder.

Prenumerationer på erbjudande nivå för hands versioner för att komma åt mellanlagrade erbjudanden:

![Förhandsgranska prenumerations-ID: n](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

När erbjudandet är aktivt kommer endast de begränsade mål grupps prenumerationerna (som anges via manuell inmatning eller CSV) att kunna visa och distribuera den privata SKU: n. Vi rekommenderar att du **alltid inkluderar dina egna prenumerationer i den begränsade mål gruppen** för den privata SKU: n i validerings syfte.

>[!NOTE]
>För fel söknings syfte kommer Microsofts support-och teknik team också att ha åtkomst till dessa privata erbjudanden.
