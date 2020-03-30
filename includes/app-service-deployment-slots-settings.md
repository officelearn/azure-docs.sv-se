---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129698"
---
När du klonar konfigurationen från en annan distributionsplats kan den klonade konfigurationen redigeras. Vissa konfigurationselement följer innehållet över en swap (inte platsspecifik), medan andra konfigurationselement stannar i samma plats efter en swap (kortplatsspecifik). Följande listor visar de inställningar som ändras när du byter fack.

**Inställningar som byts ut:**

* Allmänna inställningar, till exempel ramversion, 32/64-bitars, webbuttag
* Appinställningar (kan konfigureras för att hålla sig till en plats)
* Anslutningssträngar (kan konfigureras för att hålla sig till en plats)
* Mappningar av hanterare
* Offentliga certifikat
* WebJobs-innehåll
* Hybridanslutningar *
* Integrering av virtuella nätverk *
* Slutpunkter för tjänsten *
* Azure-innehållsleveransnätverk *

Funktioner som är markerade med en asterisk (*) planeras att tas bort. 

**Inställningar som inte byts ut:**

* Publicering av slutpunkter
* Egna domännamn
* Icke-offentliga certifikat och TLS/SSL-inställningar
* Skalningsinställningar
* WebJobs schemaläggare
* IP-begränsningar
* Alltid på
* Inställningar för diagnostiklogg
* Gemensamt för resursdelning (CORS)

> [!NOTE]
> Vissa appinställningar som gäller för oåtkomliga inställningar byts inte heller ut. Till exempel, eftersom diagnostiklogginställningar inte byts `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ut, relaterade appinställningar som och inte heller byts ut, även om de inte visas som platsinställningar.
>
