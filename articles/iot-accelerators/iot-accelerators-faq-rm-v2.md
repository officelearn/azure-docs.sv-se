---
title: Vanliga frågor och svar om anslutningslösning för fjärrövervakning – Azure | Microsoft-dokument
description: Den här artikeln besvarar de vanligaste frågorna för snabbacceleratorer för fjärrövervakningslösningar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826240"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Vanliga frågor och svar för lösningsaccelerator för fjärrövervakning

Se även den allmänna [FAQ](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Hur mycket kostar det att etablera den nya lösningen för fjärrövervakning?

Den nya lösningsacceleratorn erbjuder två distributionsalternativ:

* Ett *grundläggande* alternativ som utformats för utvecklare som letar efter lägre utvecklingskostnad eller kunder som vill bygga en demo eller proof of concept.
* Ett *standardalternativ* som utformats för företag som vill distribuera en produktionsklar infrastruktur.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hur kan jag se till att jag håller nere mina kostnader medan jag utvecklar min lösning?

Förutom att tillhandahålla två differentierade distributioner har den nya lösningen för fjärrövervakning en inställning för att aktivera eller inaktivera alla simulerade enheter på begäran. Om du inaktiverar simuleringen minskar de data som förtärs i lösningen och därmed den totala kostnaden.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Vad är skillnaden mellan de grundläggande och standarddistributionsalternativen? Hur bestämmer jag mellan de två distributionsalternativen?

Varje distributionsalternativ svarar mot olika behov. Den grundläggande distributionen är utformad för att komma igång och utveckla PoC och små piloter. Det ger en strömlinjeformad arkitektur med minsta nödvändiga resurser och en lägre kostnad. Standarddistributionen är utformad för att skapa och anpassa en produktionsklar lösning och tillhandahåller en distribution med de nödvändiga elementen för att inse det. För tillförlitlighet och skala, program mikrotjänster byggs som Docker behållare och distribueras med hjälp av en orchestrator (Kubernetes som standard). Orchestrator ansvarar för distribution, skalning och hantering av programmet. Du bör välja ett alternativ baserat på dina aktuella behov. Du kan använda den ena, den andra eller en kombination av båda beroende på projektfasen.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hur konfigurerar jag en dynamisk karta på instrumentpanelen?

Mer information finns i [Uppgradera kartnyckel för att se enheter på en dynamisk karta](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Var hittar jag information om den tidigare versionen av lösningen för fjärrövervakning?

Den tidigare versionen av lösningsacceleratorn för fjärrövervakning kallades den förkonfigurerade lösningen för fjärrövervakning i IoT Suite. Du hittar den arkiverade [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)dokumentationen på .

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera accelerator för ansluten fabrikslösning](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
