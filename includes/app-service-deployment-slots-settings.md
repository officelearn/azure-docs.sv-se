---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623709"
---
När du klonar konfiguration från en annan distributions plats kan den klonade konfigurationen redige ras. Vissa konfigurations element följer innehållet i en växling (inte en viss plats), medan andra konfigurations element stannar kvar på samma plats efter en växling (plats Specific). I följande listor visas de inställningar som ändras när du byter platser.

**Inställningar som växlas**:

* Allmänna inställningar, till exempel Ramverks version, 32/64-bitars, Web Sockets
* Appinställningar (kan konfigureras för att fästa på en plats)
* Anslutnings strängar (kan konfigureras att fästa mot en plats)
* Hanterarmappningar
* Inställningar för övervakning och diagnostik
* Offentliga certifikat
* WebJobs-innehåll
* Hybrid anslutningar *
* Integrering av virtuella nätverk *
* Tjänst slut punkter *
* Azure Content Delivery Network *

Funktioner som har marker ATS med en asterisk (*) planeras att bli tröga till plats. 

**Inställningar som inte**har växlats:

* Publicerings slut punkter
* Anpassade domännamn
* Privata certifikat och SSL-bindningar
* Skalningsinställningar
* Jobb scheman för WebJobs
* IP-begränsningar
* Alltid på
* Protokoll inställningar (HTTPS, TLS-version, klient certifikat)
* Inställningar för diagnostikloggar
* Resurs delning mellan ursprung (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->