---
title: Skapa en Azure HPC-cache (för hands version)
description: Så här skapar du en Azure HPC cache-instans
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 1cc77b24c96514f40c86115f7d611076facd406b
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181035"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Skapa en Azure HPC-cache (för hands version)

Använd Azure Portal för att skapa din cache. 

![skärm bild av cache-översikten i Azure Portal med knappen Skapa längst ned](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärm bild av sidan projekt information i Azure Portal](media/hpc-cache-create-basics.png)

I **projekt information**väljer du den prenumeration och resurs grupp som ska vara värd för Azure HPC-cachen. Se till att prenumerationen finns i listan över för [hands version](hpc-cache-prereqs.md#azure-subscription) .

I **tjänst information**anger du cache-namn och följande attribut:

* Plats – Välj en av de [regioner som stöds](hpc-cache-overview.md#region-availability).
* Virtuellt nätverk – du kan välja ett befintligt namn eller skapa ett nytt virtuellt nätverk.
* Undernät – Välj eller skapa ett undernät med minst 64 IP-adresser (/24) som endast ska användas för Azure HPC-cache.

## <a name="set-cache-capacity"></a>Ange cache-kapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På sidan **cache** måste du ange kapaciteten för Azure HPC-cachen. Det här värdet avgör hur mycket data ditt cacheminne kan innehålla och hur snabbt det kan betjäna klient begär Anden. Efter den offentliga för hands perioden kommer kapaciteten också att påverka cachens kostnad.

Cache-kapaciteten mäts i in-/utdata-åtgärder per sekund (IOPS). Välj kapacitet genom att ange följande två värden:

* Maximal data överförings hastighet för cachen (data flöde), i GB/SEK
* Mängden lagrings utrymme som allokerats för cachelagrade data i TB

Välj något av de tillgängliga data flödes värdena och cache Storage-storlekarna. IOPS-kapaciteten beräknas och visas under värde markeringarna.

Tänk på att den faktiska data överförings hastigheten är beroende av arbets belastning, nätverks hastigheter och typen av lagrings mål. Om en fil inte finns i cacheminnet, eller om den har marker ATS som inaktuell, kommer tjänsten att använda viss genom strömning för att hämta den från Server dels lagringen. Värdet du väljer ställer in maximalt data flöde för hela cacheminnet och inte alla är tillgängligt för klient begär Anden.

I cache-lagring hanterar Azure HPC cache vilka filer som cachelagras och förinstalleras för att maximera träffar i cacheträffar. Innehållet i cachen utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de används mindre ofta. Välj en lagrings storlek för cachen som enkelt kan lagra den aktiva uppsättningen arbetsfiler med ytterligare utrymme för metadata och andra kostnader.

![skärm bild av sidan cache-storlek](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Lägg till lagrings mål

Lagrings mål är Server delen och långsiktig lagring för innehållet i din cache.

Du kan definiera lagrings mål när du skapar cachen, men du kan också lägga till dem efteråt med länken i avsnittet **Konfigurera** i sidans cacheminne i portalen.

![skärm bild av sidan lagrings mål](media/hpc-cache-storage-targets-pop.png)

Klicka på **länken Lägg till lagrings mål** för att definiera dina Server dels lagrings system. Lagrings utrymmet kan vara Azure Blob-behållare eller lokala NFS-system.

Du kan definiera upp till tio olika lagrings mål.

Steg för steg-instruktioner för att lägga till ett lagrings mål ingår i [Lägg till lagrings mål](hpc-cache-add-storage.md). Proceduren skiljer sig för Blob Storage eller för NFS-export.

Här följer några tips: 

* För båda typerna av lagrings utrymme måste du ange hur du vill hitta server dels lagrings systemet (antingen en NFS-adress eller ett BLOB-behållarobjekt) och sökvägen till den klient-motstående namn rymden.

* När du skapar ett Blob Storage-mål kontrollerar du att cachen har åtkomst behörighet till lagrings kontot, enligt beskrivningen i [Lägg till åtkomst kontroll roller](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Om du inte är säker på att roll konfigurationen kommer att lyckas skapar du cachen först och lägger sedan till blob-lagringen efteråt.

* När du skapar ett NFS-lagrings mål anger du en [användnings modell](hpc-cache-add-storage.md#choose-a-usage-model). Inställningen användnings modell hjälper cacheminnet att optimera ditt arbets flöde.

## <a name="add-resource-tags-optional"></a>Lägg till resurs koder (valfritt)

På sidan **taggar** kan du lägga till [resurs Taggar](https://go.microsoft.com/fwlink/?linkid=873112) i Azure HPC-cachen. 

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cacheminnet

När du har konfigurerat den nya cachen klickar du på fliken **Granska + skapa** . Portalen verifierar dina val och gör att du kan granska dina val. Om allt stämmer klickar du på **skapa**. 

Det tar cirka 10 minuter att skapa cacheminnet. Du kan följa förloppet i Azure Portal meddelande panel. 

![skärm bild av sidorna för att skapa "distribution pågår" och "Notifications" i portalen](media/hpc-cache-deploy-status.png)

När en avisering har skapats visas ett meddelande med en länk till den nya Azure HPC cache-instansen och cachen visas i prenumerationens lista över **resurser** . 

![skärm bild av Azure HPC cache-instansen i Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Nästa steg

När din cache har visats i listan **resurser** kan du montera den för klient åtkomst, använda den för att flytta dina aktiva data till ett nytt Azure Blob Storage-mål eller definiera ytterligare data källor.

* [Montera Azure HPC-cachen](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage för Azure HPC-cache](hpc-cache-ingest.md)
* [Lägg till lagrings mål](hpc-cache-add-storage.md)
