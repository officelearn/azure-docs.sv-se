---
title: Fjärråtkomst övervakning solution accelerator vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om Fjärrövervaknings solution accelerator
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: d1cc260710d025428a1ca77c41c104dc172447e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Vanliga frågor om Fjärrövervaknings solution accelerator

Se även allmänna [vanliga frågor och svar](iot-suite-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Hur mycket kostar det för att etablera nya remote övervakningslösning?

Nya solution accelerator erbjuder två distributionsalternativ:

* En *grundläggande* alternativ som har utformats för utvecklare som söker efter lägre kostnader för utveckling eller kunder som planerar för att skapa ett demo eller konceptbevis.
* En *standard* alternativ som har utformats för företag som vill distribuera en infrastruktur för klar för produktion.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hur kan jag se till att jag Håll min nere kostnaderna när jag utveckla min lösning?

Förutom att tillhandahålla två differentierade distributioner har nya remote övervakningslösning en inställning för att aktivera eller inaktivera simulerade enheterna på begäran. Inaktivera simuleringen minskar mängden data som inhämtas i lösningen och därmed den sammanlagda kostnaden.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Vad är skillnaden mellan basic och standard distributionsalternativ? Hur ska du välja mellan två distributionsalternativ?

Varje distributionsalternativ svarar på olika behov. Den grundläggande distributionen är utformat för att komma igång och utveckla PoC och små piloter. Det ger en effektiv arkitektur med minsta nödvändiga resurser och en lägre kostnad. Standarddistributionen är utformat för att skapa och anpassa en produktionsklara lösning och ger en distribution med nödvändigt att Tänk på att. För tillförlitlighet och skala program mikrotjänster skapas som Docker-behållare och distribueras via en orchestrator (Kubernetes som standard). Orchestrator är ansvarig för distribution, skalning och hanteringen av programmet. Du bör välja ett alternativ baserat på din aktuella behov. Du kan använda en, den andra eller en kombination av båda beroende på din projektfasen.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hur konfigurerar jag en dynamisk karta på instrumentpanelen?

Mer information finns i [uppgradera kartan för att se enheter på en dynamisk karta](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska några av de andra funktionerna och funktioner i IoT solution Accelerator:

* [Utforska funktionerna i fjärråtkomst övervakning solution accelerator](iot-suite-remote-monitoring-explore.md)
* [Förutsägande Underhåll solution accelerator: översikt](iot-suite-predictive-overview.md)
* [Anslutna Factory solution accelerator översikt](iot-suite-connected-factory-overview.md)
* [IoT-säkerhet från grunden](securing-iot-ground-up.md)