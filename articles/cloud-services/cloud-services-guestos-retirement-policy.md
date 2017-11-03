---
title: "Support och tillbakadragning princip Installationshandbok för Azure-Gästoperativsystem | Microsoft Docs"
description: "Innehåller information om vad Microsoft stöder när det gäller att Azure-Gästoperativsystem som används av molntjänster."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: 4bc2d57cf4c7d6e0981aa1a5c7c989860600b897
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure-Gästoperativsystem support och tillbakadragning princip
Information om den här sidan gäller för Azure gästoperativsystemet ([Gästoperativsystem](cloud-services-guestos-update-matrix.md)) för molntjänster worker och webbtjänst roller (PaaS). Den gäller inte för virtuella datorer (IaaS).

Microsoft har en publicerade [Supportpolicy för gäst-OS](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Sidan du läser nu beskriver hur principen har implementerats.

Principen är

1. Microsoft stöder **minst de senaste två familjer av gäst-OS**. När en familj har dragits tillbaka har kunder 12 månader från det officiella pensionering att uppdatera till en nyare stöds gäst-OS-familj.
2. Microsoft stöder **minst de senaste två versionerna av stöds gäst-OS-familjer**.
3. Microsoft stöder **minst de senaste två versionerna av Azure SDK**. När en version av SDK har dragits tillbaka har kunder 12 månader från det officiella pensionering att uppdatera till en nyare version.

Ibland kan kan fler än två familjer eller versioner stödjas. Officiell Gästoperativsystem supportinformation visas på den [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-family-or-version-is-retired"></a>När en gäst-OS-familjen eller version är inaktiverad
En ny Gästoperativsystem **familj** introduceras stund efter lanseringen av en ny officiella version av operativsystemet Windows Server. När en ny gäst-OS-familj introduceras Microsoft dra tillbaka den äldsta gäst-OS-familjen.

Nya Gästoperativsystem **versioner** introduceras om varje månad för att få de senaste uppdateringarna för MSRC. På grund av de månatliga regelbundet är en gäst-OS-version normalt inaktiverad cirka 60 dagar efter versionen. Den här aktiviteten för att hålla minst två gäst-OS-versioner för varje familj som är tillgängliga för användning.

### <a name="process-during-a-guest-os-family-retirement"></a>Bearbeta under en gäst-OS-familjen pensionering
När upphör har meddelats har kunder en 12 månad ”” övergångsperioden innan äldre familjen officiellt tas bort från tjänsten. Den här övergångstid förlängas enligt Microsoft gottfinnande. Uppdateringar publiceras på den [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).

En stegvis pensionering process börjar sex (6) månader i övergångsperioden. Under denna tid:

1. Microsoft meddelar kunder i upphör.
2. Den nya versionen av Azure SDK stöder inte tillbakadragna gäst-OS-familjen.
3. Nya distributioner och redeployments molntjänster tillåts inte på tillbakadragna familjen

Microsoft fortsätter att införa nya gäst-OS-version innehåller de senaste uppdateringarna MSRC förrän den sista dagen i övergångsperioden kallas ”förfallodatum”. På giltighetstiden kommer stöds inte under Azure-serviceavtalet molntjänster fortfarande körs. Microsoft har frihet att tvinga uppgraderingen, ta bort eller stoppa tjänsterna efter detta datum.

### <a name="process-during-a-guest-os-version-retirement"></a>Bearbeta under en gäst-OS-Version ur bruk
Om kunder sina Gästoperativsystem för automatisk uppdatering kan ha de aldrig bekymra dig om hur du hanterar gäst-OS-versioner. De kommer alltid att använda den senaste gäst-OS-versionen.

Gäst-OS-versioner släpps varje månad. På grund av mängden reguljära versioner har varje version en fast livslängd.

60 dagar i livslängden är en version ”*inaktiverad*”. ”Inaktiverat” innebär att versionen har tagits bort från portalen. Versionen kan inte längre anges i CSCFG-konfigurationsfilen. Befintliga distributioner lämnas körs. Men nya distributioner och koden och konfigurationen uppdateringar av befintliga distributioner kommer att tillåtas.

Tid när du har blivit ”inaktiverad” gäst-OS-version ”*upphör att gälla*” och alla installationer som fortfarande kör den här versionen är kraft uppgraderas och ange att automatiskt uppdatera gäst-OS i framtiden. Förfallodatum görs i batchar så tidsperiod från avstängning upphör att gälla kan variera.

Dessa punkter får göras längre Microsofts gottfinnande förenklar kunden övergångar. Ändringarna kommer att få på den [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Meddelanden under ur bruk
* **Family pensionering** <br>Microsoft använder blogginlägg och portalmeddelandet. Kunder som fortfarande använder en pensionerad gäst-OS-familj meddelas via direkt kommunikation (e-post, portal meddelanden, telefonsamtal) till tilldelade tjänstadministratörer. Alla ändringar som skickas till den [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).
* **Version ur bruk** <br>Alla ändringar och de tidpunkter som de sker skickas till den [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md), inklusive versionen, inaktiverad och upphör att gälla. Tjänster-administratörer får e-post om de har distributioner som körs på en inaktiverad gäst-OS-version eller familjen. Tidtagningen av dessa e-postmeddelanden kan variera. I allmänhet är de minst en månad före avstängning, även om den här tidpunkten inte är ett officiella SLA.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Hur kan jag för att minimera konsekvenserna av migrering?**

Vi rekommenderar att du använder senaste gäst-OS-familjen för att utforma din molntjänster.

1. Börja planera migrering till en nyare familj tidigt.
2. Konfigurera tillfälliga testdistributioner för att testa din molntjänst som körs på den nya familjen.
3. Ange din gäst-OS-version till **automatisk** (osVersion = * i det [.cscfg](cloud-services-model-and-package.md#cscfg) fil) så migreringen till nya gäst-OS-versioner sker automatiskt.

**Vad händer om min webbprogram kräver djupare integrering med Operativsystemet?**

Om din web programarkitektur är beroende av underliggande funktioner i operativsystemet, använda plattformsfunktioner stöds som [Start uppgifter](cloud-services-startup-tasks.md) eller andra utökningsbarhet mekanismer. Du kan också använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS-infrastruktur som en tjänst), där du ansvarar för att underhålla det underliggande operativsystemet.

## <a name="next-steps"></a>Nästa steg
Granska senast [Gästoperativsystem släpper](cloud-services-guestos-update-matrix.md).
