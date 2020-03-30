---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187289"
---
I det här steget skapar du en brandväggsregel för att öppna avsökningsporten för den belastningsbalanserade slutpunkten (59999, som tidigare angivets) och en annan regel för att öppna tillgänglighetsgrupplyssnadporten. Eftersom du har skapat den belastningsbalanserade slutpunkten på de virtuella datorerna som innehåller tillgänglighetsgrupprepliker måste du öppna avsökningsporten och lyssnarporten på respektive virtuella datorer.

1. På virtuella datorer som är värdar för repliker startar du **Windows-brandväggen med avancerad säkerhet**.

2. Högerklicka på **Inkommande regler**och klicka sedan på Ny **regel**.

3. På sidan **Regeltyp** väljer du **Port**och klickar sedan på **Nästa**.

4. På sidan **Protokoll och Portar** väljer du **TCP**, skriver **59999** i rutan **Specifika lokala portar** och klickar sedan på **Nästa**.

5. Behåll **Tillåt anslutningen** markerad på sidan **Åtgärd** och klicka sedan på **Nästa**.

6. På sidan **Profil** godkänner du standardinställningarna och klickar sedan på **Nästa**.

7. Ange ett regelnamn i textrutan **Namn** på sidan **Namn,** till exempel **Alltid på avsökningsporten**för lyssnaren och klicka sedan på **Slutför**.

8. Upprepa föregående steg för tillgänglighetsgruppens lyssnarport (som angavs tidigare i parametern $EndpointPort för skriptet) och ange sedan ett lämpligt regelnamn, till exempel **Alltid på lyssnarporten**.

