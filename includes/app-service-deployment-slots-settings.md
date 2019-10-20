---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "71129698"
---
När du klonar konfiguration från en annan distributions plats kan den klonade konfigurationen redige ras. Vissa konfigurations element följer innehållet i en växling (inte en viss plats), medan andra konfigurations element stannar kvar på samma plats efter en växling (plats Specific). I följande listor visas de inställningar som ändras när du byter platser.

**Inställningar som växlas**:

* Allmänna inställningar, till exempel Ramverks version, 32/64-bitars, Web Sockets
* Appinställningar (kan konfigureras för att fästa på en plats)
* Anslutnings strängar (kan konfigureras att fästa mot en plats)
* Mappningar för hanterare
* Offentliga certifikat
* WebJobs-innehåll
* Hybrid anslutningar *
* Integrering av virtuella nätverk *
* Tjänst slut punkter *
* Azure Content Delivery Network *

Funktioner som har marker ATS med en asterisk (*) planeras att inte växlas. 

**Inställningar som inte har växlats**:

* Publicerings slut punkter
* Egna domännamn
* Icke-offentliga certifikat och TLS/SSL-inställningar
* Skalnings inställningar
* Jobb scheman för WebJobs
* IP-begränsningar
* Alltid på
* Inställningar för diagnostikloggar
* Resurs delning mellan ursprung (CORS)

> [!NOTE]
> Vissa inställningar för appar som tillämpas på inställningar som inte har växlats växlas inte heller. Till exempel, eftersom inställningarna för diagnostikloggar inte växlas, växlas inte relaterade appinställningar som `WEBSITE_HTTPLOGGING_RETENTION_DAYS` och `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` även om de inte visas som plats inställningar.
>
