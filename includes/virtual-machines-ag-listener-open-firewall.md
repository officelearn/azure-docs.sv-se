---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097701"
---
I det här steget ska du skapa en brandväggsregel för att öppna avsökningsporten för slutpunkten för Utjämning av nätverksbelastning (enligt vad som anges tidigare 59999) och en annan regel för att öppna porten för tillgänglighetsgruppens lyssnare. Eftersom du har skapat den belastningsutjämnade slutpunkten på de virtuella datorerna som innehåller tillgänglighetsgrupprepliker måste du öppna avsökningsporten och lyssningsport på respektive virtuella datorer.

1. Starta på virtuella datorer som är värdar för repliker, **Windows-brandväggen med avancerad säkerhet**.

2. Högerklicka på **regler för inkommande trafik**, och klicka sedan på **ny regel**.

3. På den **regeltyp** väljer **Port**, och klicka sedan på **nästa**.

4. På den **protokoll och portar** väljer **TCP**, typ **59999** i den **specifika lokala portar** rutan och klicka sedan på  **Nästa**.

5. På den **åtgärd** behåller **Tillåt anslutningen** markerad och klicka sedan på **nästa**.

6. På den **profil** sidan, accepterar du standardinställningarna och klicka sedan på **nästa**.

7. På den **namn** sidan den **namn** text, anger du ett namn för regeln som **alltid på avsökning lyssningsport**, och klicka sedan på **Slutför**.

8. Upprepa föregående steg för porten för tillgänglighetsgruppens lyssnare (som anges tidigare i parametern $EndpointPort för skriptet) och ange sedan ett lämpligt namn, till exempel **alltid på lyssningsport**.

<!-- Update_Description: update meta properties -->