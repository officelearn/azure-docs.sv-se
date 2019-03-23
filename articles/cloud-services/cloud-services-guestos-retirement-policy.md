---
title: Support och indragningsprincip guide för Azures gäst-OS | Microsoft Docs
description: Innehåller information om vad Microsoft stöder beträffande till Azures gäst-OS som används av molntjänster.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: ce66d44c0ddb84ed8c2908d02b8062195d6b461d
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351024"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure gäst-OS-support och tillbakadragande princip
Information om den här sidan som relaterar till Azure gästoperativsystemet ([gäst-OS](cloud-services-guestos-update-matrix.md)) för molntjänster worker och webbtjänst roller (PaaS). Den gäller inte för virtuella datorer (IaaS).

Microsoft har en publiceringswebbkod [Supportpolicy för Gästoperativsystemet i](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). På sidan som du läser nu beskriver hur principen har implementerats.

Principen är

1. Microsoft stöder **minst de senaste två familjerna av gäst-OS**. När en serie som har dragits tillbaka har kunder 12 månader från den officiella slutdatum att uppdatera till en nyare stöds-OS-gästoperativsystemfamilj.
2. Microsoft stöder **minst de senaste två versionerna av stöds gästoperativsystemfamiljer**.
3. Microsoft stöder **minst de senaste två versionerna av Azure SDK**. När en version av SDK: N har dragits tillbaka har kunder 12 månader från datumet för officiella tillbakadragandet att uppdatera till en nyare version.

Ibland kan mer än två familjer, eller versioner stödjas. Officiella gäst-OS-supportinformation visas på den [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>När en gäst-OS-version har dragits tillbaka
Ny gäst-OS **versioner** introduceras om varje månad att införliva de senaste MSRC-uppdateringarna. På grund av de regelbundna månatliga uppdateringarna inaktiveras normalt en gäst-OS-version cirka 60 dagar efter versionen. Den här aktiviteten ser till att minst två gäst-OS-versioner för respektive familj som är tillgängliga för användning.

### <a name="process-during-a-guest-os-family-retirement"></a>Processen under en family gästoperativsystem, indragning
När indragningen har meddelats har kunder en 12-månaders ”” övergångsperiod innan äldre familjen officiellt tas bort från tjänsten. Den här övergångstid förlängas gottfinnande av Microsoft. Uppdateringar kommer att publiceras på den [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).

En stegvis tillbakadragande process börjar sex (6) månader övergångsperioden. Under denna tid:

1. Microsoft meddelar kunderna av indragningen.
2. Indragna gästoperativsystemfamilj stöd inte för den nya versionen av Azure SDK.
3. Nya distributioner och redeployments molntjänster tillåts inte på den indragna familjen

Microsoft fortsätter att introducera nya gäst-OS-versionen införliva de senaste uppdateringarna MSRC förrän den sista dagen i övergångsperioden kallas ”förfallodatum”. På förfallodatumet, kommer inte stöds under serviceavtalet för Azure molntjänster som fortfarande körs. Microsoft har frihet att tvinga uppgraderingen tar du bort eller stoppa tjänsterna efter detta datum.

### <a name="process-during-a-guest-os-version-retirement"></a>Processen under en gäst-OS-Version pensionering
Om kunder har sina gäst-OS för att automatiskt uppdatera behöver de aldrig bekymra dig om hantering av gäst-OS-versioner. De kommer alltid att använda den senaste gäst-OS-versionen.

Gäst-OS-versioner släpps varje månad. På grund av mängden vanliga versioner har varje version en fast livslängd.

60 dagar till livslängden för en version är ”*inaktiverad*”. ”Inaktiverad” innebär att versionen har tagits bort från portalen. Versionen kan inte längre anges i CSCFG-konfigurationsfilen. Befintliga distributioner är igång. Men nya distributioner och kod och konfiguration uppdateringar av befintliga distributioner kommer att tillåtas.

Någon gång när du har blivit ”inaktiverad”, Gäst-OS-version ”upphör att gälla” och alla installationer som fortfarande körs den utgångna versionen exponeras för säkerhets- och säkerhetsproblem problem. Normalt görs upphör att gälla i batchar, så perioden Invalid-upphör att gälla kan variera.

Kunder som konfigurerar sina tjänster för att uppdatera gäst-OS manuellt, bör se till att deras tjänster körs på ett Gästoperativsystem som stöds. Om en tjänst är konfigurerad för att automatiskt uppdatera gäst-OS, säkerställa att den underliggande plattformen och uppgraderar till den senaste gäst-OS.

De här perioderna kan göras längre enligt Microsofts bedömning för att underlätta övergången för kund. Alla ändringar som ska förmedlas på den [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Meddelanden under pensionering
* **Programvarufamiljer pensionering** <br>Microsoft använder blogginlägg och portal-meddelande. Kunder som fortfarande använder en pensionerad gästoperativsystemfamilj kommer att meddelas via direktkommunikation (e-post, portal meddelanden, telefonsamtal) till tilldelade tjänstadministratörer. Alla ändringar som ska skickas till den [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).
* **Version Retirement** <br>Alla ändringar och datumen de inträffar ska skickas till den [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md), inklusive versionen, inaktiverad och upphör att gälla. Services-administratörer får e-postmeddelanden om de har distributioner som körs på en inaktiverad gäst-OS-version eller en serie. Tidsinställningen för dessa e-postmeddelanden kan variera. I allmänhet är de minst en månad innan avstängning, även om den här tiden inte är något officiellt serviceavtal.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Hur kan jag för att minimera påverkan av migrering?**

Vi rekommenderar att du använder senaste gästoperativsystemfamilj för att utforma dina molntjänster.

1. Börja planera din migrering till en nyare familj tidigt.
2. Ställ in tillfälliga testdistributioner för att testa din molntjänst som körs på ny serie.
3. Ange din gäst-OS-version **automatisk** (osVersion = * i den [.cscfg](cloud-services-model-and-package.md#cscfg) fil) så migreringen till nya gäst-OS-versioner sker automatiskt.

**Vad händer om min webbprogrammet kräver djupare integrering med Operativsystemet?**

Om din webbprogramarkitektur är beroende av underliggande funktioner i operativsystemet, använda funktioner för plattform som stöds som [startåtgärder](cloud-services-startup-tasks.md) och andra mekanismer som utökningsbarhet. Du kan också använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktur som en tjänst), där du ansvarar för att det underliggande operativsystemet.

## <a name="next-steps"></a>Nästa steg
Granska senast [Guest OS-versionerna](cloud-services-guestos-update-matrix.md).
