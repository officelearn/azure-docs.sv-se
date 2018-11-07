---
title: 'Privat SKU: er | Microsoft Docs'
description: 'Hur du använder privata SKU: er för att hantera tillgängligheten för erbjudandet.'
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 28fe85c6443701d5fb40d4f90dbec9ba445a234d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230248"
---
<a name="private-skus"></a>Privat SKU: er
============

Privat SKU: er kan du begränsa tillgängligheten för SKU: er till specifika kunder. När en SKU har markerats som privat, det är inte tillgängligt i alla offentliga katalogen, inklusive på [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](http://portal.azure.com). På Azure-portalen Se bara kunder med åtkomst till SKU: N den. Dessutom kan ombeds de också att de har åtkomst till privata erbjudanden.

>[!NOTE]
>Privat SKU: er måste ha nya unika SKU /-planen ID: N att undvika eventuella konflikter med dina offentliga SKU: er.

Du kan använda privata SKU: er för att hantera följande scenarier:

1.  Publicera program som du vill endast tillgänglig offentligt för specifika kunder och inte allmänt tillgängliga.
2.  Publicera varianter av offentliga programvara till en anpassad pris för specifika kunder.
3.  Publicera varianter av offentliga programvara med en anpassad beskrivning och villkor (via nya erbjudandet).

Du kan återanvända diskarna från en annan SKU: N i samma erbjudandet om du bara vill ändra priset. Med privata SKU: er har du inte skicka diskar i SKU: er.

<a name="mark-a-sku-private"></a>Markera en SKU-privat
---------------------

Ändra alternativet frågar om SKU: N är privat om du vill markera en SKU som privat:

![Markera en SKU som privat](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Du kan återanvända diskarna i en annan SKU och ändra prissättningen eller beskrivningen. Om du vill återanvända diskarna, Välj **Ja** som svar på en ”inte den här SKU återanvända bilder från en offentlig SKU” fråga.

Om SKU: N har markerats som privat och erbjudandet har andra SKU: er med reuseable diskar, behöver du indikera att SKU: N återanvänder diskar från en annan SKU. Du måste också ange vilken målgrupp privat SKU: N.

>[!NOTE]
>När den har publicerats kan inte en offentlig SKU göras privata.

<a name="select-an-image"></a>Välj en avbildning
------------------

Du kan ange nya diskar för den privata SKU eller återanvända samma diskarna redan i en annan SKU, endast ändra prissättningen eller beskrivning. Om du vill återanvända diskarna, Välj **Ja** som ett svar på frågan ”gör den här SKU återanvända avbildningen från en offentlig SKU”.

![Ange bild återanvända](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

När du har bekräftat att SKU: N återanvänder bilder från en annan SKU, kan du identifiera den SKU som är källan för avbildningarna.

Anvisningarna i nästa skärm avbilda visar hur du identifierar privata SKU skulle återanvända bilder från den valda SKU: N:

![Välj en avbildning](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

När du publicerar erbjudandet skulle bilder från den valda SKU vara tillgängliga under den privata SKU-ID med anpassade priser/villkor. Privata SKU: N bara är synliga för målgruppen.

För uppdateringar av avbildningar skulle du bara behöva uppdatera den underliggande SKU-avbildning. I bakgrunden kommer avbildningen i privata SKU: n också att uppdateras automatiskt. På samma sätt, om du tar bort bilden från den underliggande SKU bilden skulle också tas bort från privata SKU: N.

<a name="restricting-the-audience"></a>Att begränsa målgruppen
------------------------

Privata erbjudanden kan hitta och distribueras bara av målanvändare.
Vi stöder för närvarande målobjekt användare som använder prenumerations-ID.

Dessa prenumerationer kan anges via ett formulär för manuell inmatning **för upp till 10 prenumerationer**, eller genom att ladda upp en CSV-fil, vilket gör att **för upp till 20 000 prenumerationer**.

Manuell inmatning för begränsad publik:

![Begränsa manuellt målgrupp](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV överför för begränsad publik:

![Använd CSV för att begränsa målgrupp](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Exemplet CSV-filinnehåll:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

När du växlar från manuell inmatning till CSV överför vy eller från CSV till manuell inmatning gamla listan över prenumerations-ID med åtkomst till SKU: N har sparats inte. En varning visas och listan bara skrivas över vid sparar erbjudandet.

<a name="previewing-private-offers"></a>Förhandsgranska privata erbjudanden
-------------------------

Under förhandsversionen/mellanlagring steg kan endast erbjudandet på preview-prenumerationer kan komma åt SKU: N. Detta testar scenen då du kan verifiera vilken erbjudandet skulle se ut till kunderna riktade och är standard för alla typer av publicering.

Erbjud nivå förhandsversion prenumerationer för att komma åt mellanlagrad erbjudanden:

![Förhandsversion av prenumerations-ID: N](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

När erbjudandet går live får kommer endast begränsad publik prenumerationerna (angetts via manuell inmatning eller CSV) att kunna visa och distribuera privata SKU: N. Vi rekommenderar att du **ta alltid med dina egna prenumerationer i begränsad publik** för privata SKU för verifiering.

>[!NOTE]
>Felsökningssyfte Microsoft-supporten och tekniska team kan också få åtkomst till dessa privata erbjudanden.
