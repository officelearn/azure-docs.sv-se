---
title: Fjärråtkomst övervakning lösningsaccelerator vanliga frågor och svar – Azure | Microsoft Docs
description: Vanliga frågor om lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 746d21c7ff4d5b939eea7690193ac07425b4001c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447921"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Vanliga frågor om lösningsacceleratorn för fjärrövervakning

Se även allmänna [vanliga frågor och svar](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Hur mycket kostar det för att etablera den nya lösningen för fjärrövervakning?

Ny solution accelerator erbjuder två distributionsalternativ:

* En *grundläggande* alternativ för utvecklare som söker efter lägre kostnader för utveckling eller kunder som vill skapa en demonstration eller konceptbevis.
* En *standard* alternativet utformat för företag som vill distribuera ett produktionsklart-infrastruktur.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hur kan jag att jag hålla kostnaderna nere när jag utvecklar min lösning?

Förutom att tillhandahålla två differentierad distributioner, har den nya lösningen för fjärrövervakning en inställning för att aktivera eller inaktivera de simulerade enheterna på begäran. Inaktivera simuleringen minskar mängden data som matas in i lösningen och därmed den totala kostnaden.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Vad är skillnaden mellan basic och standard distributionsalternativen? Hur avgör jag mellan två distributionsalternativ?

Varje distributionsalternativ svarar på olika behov. Den grundläggande distributionen har utformats för att komma igång och utveckla PoC och små piloter. Det ger en effektiv arkitektur minsta nödvändiga resurser och en lägre kostnad. Standarddistributionen har utformats för att skapa och anpassa en produktionsklar lösning och ger en distribution med nödvändigt för att upptäcka som. För tillförlitlighet och skala program mikrotjänster skapas med Docker-behållare och distribueras via en initierare (Kubernetes som standard). Initieraren ansvarar för distribution, skalning och hantering av appen. Du bör välja ett alternativ baserat på dina befintliga behov. Du kan använda en, den andra eller en kombination av båda beroende på din projektfasen.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hur konfigurerar jag en dynamisk karta på instrumentpanelen?

Mer information finns i [uppgradera kartan för att se enheter på en dynamisk karta](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Var hittar jag information om den tidigare versionen av lösningen för fjärrövervakning

Den tidigare versionen av lösningsacceleratorn för fjärrövervakning kallades IoT Suite av den förkonfigurerade lösningen för fjärrövervakning. Du kan hitta arkiverad dokumentation på [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera lösningsaccelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
