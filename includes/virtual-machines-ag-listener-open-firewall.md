---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187289"
---
I det här steget skapar du en brand Väggs regel för att öppna avsöknings porten för den belastningsutjämnade slut punkten (59999 som anges ovan) och en annan regel för att öppna tillgänglighets gruppens lyssnar port. Eftersom du skapade den belastningsutjämnade slut punkten på de virtuella datorer som innehåller tillgänglighets grupp repliker måste du öppna avsöknings porten och lyssnar porten på respektive virtuell dator.

1. Starta **Windows-brandväggen med avancerad säkerhet**på virtuella datorer som är värdar för repliker.

2. Högerklicka på **regler för inkommande**trafik och klicka sedan på **ny regel**.

3. På sidan **regeltyp** väljer du **port**och klickar sedan på **Nästa**.

4. På sidan **protokoll och portar** väljer du **TCP**, skriver **59999** i rutan **aktuella lokala portar** och klickar sedan på **Nästa**.

5. På sidan **åtgärd** behåller **du Tillåt anslutningen** vald och klickar sedan på **Nästa**.

6. Godkänn standardinställningarna på sidan **profil** och klicka sedan på **Nästa**.

7. På sidan **namn** , i text rutan **namn** , anger du ett regel namn, till exempel **Always on port för avlyssnare**och klickar sedan på **Slutför**.

8. Upprepa föregående steg för tillgänglighets gruppens lyssnare-port (som tidigare angavs i parametern $EndpointPort i skriptet) och ange sedan ett lämpligt regel namn, till exempel **Always on-lyssnarporten**.

