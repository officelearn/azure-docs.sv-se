---
title: Support- och pensionspolicyguide för Azure Guest OS | Microsoft-dokument
description: Innehåller information om vad Microsoft kommer att stödja när det gäller Azure Guest OS som används av Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68945456"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure Guest OS support och pensionpolicy
Informationen på den här sidan gäller Azure Guest-operativsystemet[(Guest OS)](cloud-services-guestos-update-matrix.md)för Cloud Services-arbetare och webbroller (PaaS). Det gäller inte för virtuella datorer (IaaS).

Microsoft har en publicerad [supportpolicy för Gästoperativsystemet](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Sidan du läser nu beskriver hur principen implementeras.

Policyn är

1. Microsoft kommer att stödja **minst de två senaste familjerna i Guest OS**. När en familj går i pension har kunderna 12 månader från det officiella pensionsdatumet att uppdatera till en nyare gäst-OS-familj som stöds.
2. Microsoft stöder **minst de två senaste versionerna av de gäst-OS-familjer som stöds**.
3. Microsoft stöder **åtminstone de två senaste versionerna av Azure SDK**. När en version av SDK dras tillbaka har kunderna 12 månader från det officiella pensionsdatumet att uppdatera till en nyare version.

Ibland kan mer än två familjer eller utgåvor stödjas. Officiell supportinformation för gästoperativsystem visas i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>När en Gäst-OS-version dras tillbaka
Nya Gäst **OS-versioner** introduceras ungefär varje månad för att införliva de senaste MSRC-uppdateringarna. På grund av de regelbundna månatliga uppdateringarna inaktiveras en Gäst-OS-version normalt cirka 60 dagar efter dess release. Den här aktiviteten håller minst två Gästoperativsystemversioner tillgängliga för varje familj tillgängligt för användning.

### <a name="process-during-a-guest-os-family-retirement"></a>Process under en gäst OS familj pensionering
När pensioneringen tillkännages, kunder har en 12 månaders "övergångsperiod" innan den äldre familjen officiellt tas ur drift. Denna övergångstid kan förlängas efter Microsofts gottfinnande. Uppdateringar publiceras på [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

En gradvis pensioneringsprocess kommer att inledas sex (6) månader in i övergångsperioden. Under denna tid:

1. Microsoft meddelar kunderna om pensioneringen.
2. Den nyare versionen av Azure SDK stöder inte den pensionerade Gästoperativsystemet-familjen.
3. Nya distributioner och omfördelningar av molntjänster kommer inte att tillåtas i den pensionerade familjen

Microsoft kommer att fortsätta att introducera ny Gäst OS-version som innehåller de senaste MSRC-uppdateringarna fram till den sista dagen i övergångsperioden, så kallade "utgångsdatum". På utgångsdatumet kommer molntjänster som fortfarande körs inte att stödjas under Azure SLA. Microsoft har rätt att tvinga fram uppgradering, borttagning eller stopp av dessa tjänster efter detta datum.

### <a name="process-during-a-guest-os-version-retirement"></a>Process under en gäst OS Version pensionering
Om kunderna ställer in sina gästoperativsystem för att uppdatera automatiskt behöver de aldrig oroa sig för att hantera gäst-OS-versioner. De kommer alltid att använda den senaste Guest OS-versionen.

Gäst OS-versioner släpps varje månad. På grund av graden av regelbundna utgåvor, har varje version en fast livslängd.

På 60 dagar in i livslängden, är en version "*handikappad*". "Inaktiverad" innebär att versionen tas bort från portalen. Det går inte längre att ange versionen från CSCFG-konfigurationsfilen. Befintliga distributioner körs. Men nya distributioner och kod- och konfigurationsuppdateringar till befintliga distributioner tillåts inte.

Någon gång efter att ha blivit "inaktiverad" ", Guest OS-versionen "upphör" och alla installationer som fortfarande körs som utgångna version utsätts för säkerhetsproblem och sårbarhetsproblem. I allmänhet sker förfallodatum i batchar, så perioden från inaktivering till förfallodatum kan variera.

Kunder som konfigurerar sina tjänster för att uppdatera gästoperativsystemet manuellt bör se till att deras tjänster körs på ett gästoperativsystem som stöds. Om en tjänst har konfigurerats för att uppdatera gästoperativsystemet automatiskt, kommer den underliggande plattformen att säkerställa efterlevnad och uppgradera till det senaste gästoperativsystemet.

Dessa perioder kan göras längre efter Microsofts gottfinnande för att underlätta kundövergångar. Alla ändringar kommer att meddelas på [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Anmälningar vid pensionering
* **Familjepension** <br>Microsoft kommer att använda blogginlägg och portalmeddelanden. Kunder som fortfarande använder en pensionerad gäst-OS-familj meddelas via direkt kommunikation (e-post, portalmeddelanden, telefonsamtal) till tilldelade tjänstadministratörer. Alla ändringar bokförs i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).
* **Version Pensionering** <br>Alla ändringar och datum de inträffar kommer att bokföras i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md), inklusive release, inaktiverad och förfallodatum. Tjänstadministratörer får e-postmeddelanden om de har distributioner som körs på en inaktiverad gäst-OS-version eller -familj. Tidpunkten för dessa e-postmeddelanden kan variera. Generellt de är minst en månad före invaliditet, även om denna tidpunkt är inte en officiell SLA.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Hur kan jag mildra effekterna av migrering?**

Vi rekommenderar att du använder den senaste gästoperativsystemets familj för att utforma dina molntjänster.

1. Börja planera din migrering till en nyare familj tidigt.
2. Konfigurera tillfälliga testdistributioner för att testa din molntjänst som körs på den nya familjen.
3. Ställ in gästoperativsystemets version på **Automatisk** (osVersion=* i [CSCFG-filen)](cloud-services-model-and-package.md#cscfg) så att migreringen till nya Gästoperativsystem-versioner sker automatiskt.

**Vad händer om mitt webbprogram kräver djupare integration med operativsystemet?**

Om webbprogramarkitekturen är beroende av underliggande funktioner i operativsystemet använder du plattformsstödda funktioner som [startuppgifter](cloud-services-startup-tasks.md) eller andra utökningsmekanismer. Alternativt kan du också använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure as a Service), där du är ansvarig för att underhålla det underliggande operativsystemet.

## <a name="next-steps"></a>Nästa steg
Granska de senaste [Gäst-OS-utgåvorna](cloud-services-guestos-update-matrix.md).
