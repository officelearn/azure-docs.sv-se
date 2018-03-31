---
title: Åtkomst till virtuella Azure-datorer från Server Explorer | Microsoft Docs
description: Få en översikt över hur du visar skapa och hantera virtuella Azure-datorer (VM) i Server Explorer i Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: bbb52b07994ca796683bbcfe5a77ebb78df3bade
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Åtkomst till virtuella Azure-datorer från Server Explorer

Om du har virtuella datorer som finns i Azure, kan du komma åt dem i Server Explorer. Du måste först logga in på Azure-prenumerationen att visa dina mobila tjänster. Öppna snabbmenyn för noden Azure i Server Explorer för att logga in och välj **Anslut till Microsoft Azure**.

1. Välj en virtuell dator i Cloud Explorer och klicka på F4 för att visa dess egenskapsfönstret.

    I följande tabell visas vilka egenskaper som är tillgängliga, men de är alla skrivskyddade. Du kan ändra dem i [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Egenskap | Beskrivning |
   | --- | --- |
   | DNS-namn |URL: en med Internet-adressen för den virtuella datorn. |
   | Miljö |Värdet för den här egenskapen är alltid produktion för en virtuell dator. |
   | Namn |Namnet på den virtuella datorn. |
   | Storlek |Storleken på den virtuella datorn, som visar den totala mängden minne och diskutrymme som är tillgänglig. Mer information finns i [storlekar för virtuella datorer](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Status |Värden är Start, igång, stoppas, Stoppad och hämtar Status. Om hämtning av Status visas är aktuell status okänd. Värdena för den här egenskapen skiljer sig från de värden som används på den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | Prenumerations-ID |Prenumerations-ID för ditt Azure-konto. Du kan visa den här informationen på den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) genom att visa egenskaperna för en prenumeration. |
2. Välj en slutpunkt-nod och sedan visa den **egenskaper** fönster.
3. I följande tabell beskrivs tillgängliga egenskaper för slutpunkter, men de är skrivskyddad. Lägg till eller redigera slutpunkterna för en virtuell dator genom att använda den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Egenskap | Beskrivning |
   | --- | --- |
   | Namn |En identifierare för slutpunkten. |
   | Privat Port |Port för nätverksåtkomst som är interna för ditt program. |
   | Protokoll |Det protokoll som används för Transportskiktet för den här slutpunkten TCP eller UDP. |
   | Offentlig port |Den port som används för offentlig åtkomst till ditt program. |
