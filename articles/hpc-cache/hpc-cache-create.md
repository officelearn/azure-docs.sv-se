---
title: Skapa en Azure HPC-cache (för hands version)
description: Så här skapar du en Azure HPC cache-instans
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: 7052b88a24ff5353656a71a7bfb044922ae1415c
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709982"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Skapa en Azure HPC-cache (för hands version)

Använd Azure Portal för att skapa din cache.

![skärm bild av cache-översikten i Azure Portal med knappen Skapa längst ned](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärm bild av sidan projekt information i Azure Portal](media/hpc-cache-create-basics.png)

I **projekt information**väljer du den prenumeration och resurs grupp som ska vara värd för cachen. Se till att prenumerationen finns i listan över för [hands version](hpc-cache-prereqs.md#azure-subscription) .

I **tjänst information**anger du cache-namn och följande attribut:

* Plats – Välj en av de [regioner som stöds](hpc-cache-overview.md#region-availability).
* Virtuellt nätverk – du kan välja ett befintligt namn eller skapa ett nytt virtuellt nätverk.
* Undernät – Välj eller skapa ett undernät med minst 64 IP-adresser (/24) som endast ska användas för den här Azure HPC cache-instansen.

## <a name="set-cache-capacity"></a>Ange cache-kapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På sidan **cache** måste du ange kapaciteten för din cache. Värdena som anges här avgör hur mycket data ditt cacheminne kan innehålla och hur snabbt det kan betjäna klient begär Anden.

Efter den offentliga för hands versionen kommer kapaciteten också att påverka cachens kostnad.

Välj kapacitet genom att ange följande två värden:

* Maximal data överförings hastighet för cachen (data flöde), i GB/SEK
* Mängden lagrings utrymme som allokerats för cachelagrade data i TB

Välj något av de tillgängliga data flödes värdena och cache Storage-storlekarna.

Tänk på att den faktiska data överförings hastigheten är beroende av arbets belastning, nätverks hastigheter och typen av lagrings mål. De värden du väljer ställer in maximalt data flöde för hela cache-systemet, men vissa av dem används för omkostnader. Om till exempel en klient begär en fil som inte redan finns lagrad i cacheminnet, eller om filen har marker ATS som inaktuell, använder cacheminnet en del av dess data flöde för att hämta den från Server dels lagringen.

Azure HPC cache hanterar vilka filer som cachelagras och förinstalleras för att maximera träffar i cacheträffar. Innehållet i cachen utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de används mindre ofta. Välj en lagrings storlek för cachen som enkelt kan lagra den aktiva uppsättningen arbetsfiler med ytterligare utrymme för metadata och andra kostnader.

![skärm bild av sidan cache-storlek](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Lägg till resurs koder (valfritt)

På sidan **taggar** kan du lägga till [resurs Taggar](https://go.microsoft.com/fwlink/?linkid=873112) till Azure HPC-instansen.

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cacheminnet

När du har konfigurerat den nya cachen klickar du på fliken **Granska + skapa** . Portalen verifierar dina val och gör att du kan granska dina val. Om allt stämmer klickar du på **skapa**.

Det tar cirka 10 minuter att skapa cacheminnet. Du kan följa förloppet i Azure Portal meddelande panel.

![skärm bild av sidorna för att skapa "distribution pågår" och "Notifications" i portalen](media/hpc-cache-deploy-status.png)

När en avisering har skapats visas ett meddelande med en länk till den nya Azure HPC cache-instansen och cachen visas i prenumerationens lista över **resurser** .

![skärm bild av Azure HPC cache-instansen i Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Nästa steg

När cachen visas i listan **resurser** definierar du lagrings målen för att ge cache åtkomst till dina data källor.

* [Lägg till lagrings mål](hpc-cache-add-storage.md)
