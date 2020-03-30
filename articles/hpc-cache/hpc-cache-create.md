---
title: Skapa en Azure HPC-cache
description: Skapa en Azure HPC-cacheinstans
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129558"
---
# <a name="create-an-azure-hpc-cache"></a>Skapa en Azure HPC-cache

Använd Azure-portalen för att skapa din cache.

![skärmbild av cacheöversikt i Azure-portalen, med knappen Skapa längst ned](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärmbild av sidan projektinformation i Azure-portalen](media/hpc-cache-create-basics.png)

Välj den prenumeration och resursgrupp som ska vara värd för cacheminnet i **Project Details.** Kontrollera att prenumerationen finns i [åtkomstlistan.](hpc-cache-prereqs.md#azure-subscription)

I **Serviceinformation**anger du cachenamnet och dessa andra attribut:

* Plats - Välj en av de [regioner som stöds](hpc-cache-overview.md#region-availability).
* Virtuellt nätverk - Du kan välja ett befintligt eller skapa ett nytt virtuellt nätverk.
* Undernät - Välj eller skapa ett undernät med minst 64 IP-adresser (/24) som endast ska användas för den här Azure HPC-cacheinstansen.

## <a name="set-cache-capacity"></a>Ange cachekapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På **sidan Cache** måste du ange cachens kapacitet. De värden som anges här avgör hur mycket data cachen kan innehålla och hur snabbt den kan betjäna klientbegäranden.

Kapaciteten påverkar också cachens kostnad.

Välj kapacitet genom att ange dessa två värden:

* Den maximala dataöverföringshastigheten för cacheminnet (dataflödet), i GB/sekund
* Mängden lagringsutrymme som allokerats för cachelagrade data, i TB

Välj ett av de tillgängliga dataflödesvärdena och cachelagringsstorlekarna.

Tänk på att den faktiska dataöverföringshastigheten beror på arbetsbelastning, nätverkshastigheter och typ av lagringsmål. De värden du väljer anger maximalt dataflöde för hela cachesystemet, men en del av detta används för omkostnader. Om en klient till exempel begär en fil som inte redan lagras i cacheminnet, eller om filen är markerad som inaktuell, använder cachen en del av dataflödet för att hämta den från backend-lagring.

Azure HPC Cache hanterar vilka filer som cachelagras och förinstalleras för att maximera cache träfffrekvensen. Cacheinnehållet utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de används mindre ofta. Välj en lagringsstorlek för cacheminnet som bekvämt kan innehålla den aktiva uppsättningen arbetsfiler med extra utrymme för metadata och andra omkostnader.

![skärmbild av cachestorlekssidan](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Lägga till resurstaggar (valfritt)

På sidan **Taggar** kan du lägga till [resurstaggar](https://go.microsoft.com/fwlink/?linkid=873112) i din Azure HPC-cacheinstans.

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cacheminnet

När du har konfigurerat den nya cachen klickar du på fliken **Granska + skapa.** Portalen validerar dina val och låter dig granska dina val. Om allt är korrekt klickar du på **Skapa**.

Cache skapande tar ca 10 minuter. Du kan spåra förloppet i Azure-portalens meddelandepanel.

![skärmbild av cacheskapande "distribution pågår" och "meddelanden" sidor i portalen](media/hpc-cache-deploy-status.png)

När skapandet är klart visas ett meddelande med en länk till den nya Azure HPC-cacheinstansen och cachen visas i prenumerationens **resurslista.**
<!-- double check on notification -->

![skärmbild av Azure HPC Cache-instans i Azure-portalen](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Nästa steg

När cacheminnet har visas i listan **Resurser** definierar du lagringsmål för att ge cachen åtkomst till dina datakällor.

* [Lägga till lagringsmål](hpc-cache-add-storage.md)
