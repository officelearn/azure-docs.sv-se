---
title: Vanliga frågor och svar om Remote Monitoring Solution-Azure | Microsoft Docs
description: I den här artikeln besvaras vanliga frågor och svar om lösningar för fjärr styrnings lösningar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 64391d7f5a7b1a295be7e404d27e5cbe8c691eb2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995955"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Vanliga frågor och svar om lösningar för fjärr styrnings lösningar

Se även [vanliga frågor och svar](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Hur mycket kostar det att etablera den nya lösningen för fjärrövervakning?

Den nya Solution Accelerator erbjuder två distributions alternativ:

* Ett *grundläggande* alternativ som är avsett för utvecklare som vill ha lägre utvecklings kostnader eller kunder som vill bygga en demonstration eller ett koncept bevis.
* Ett *standard* alternativ som är avsett för företag som vill distribuera en produktions färdig infrastruktur.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hur kan jag se till att mina kostnader hålls nere medan jag utvecklar lösningen?

Förutom att tillhandahålla två differentierade distributioner, har den nya fjärr styrnings lösningen en inställning för att aktivera eller inaktivera alla simulerade enheter på begäran. Genom att inaktivera simuleringen minskar du de data som matas in i lösningen och därmed den totala kostnaden.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Vad är skillnaden mellan alternativen för Basic-och standard-distribution? Hur gör jag för att välja mellan de två distributions alternativen?

Varje distributions alternativ svarar på olika behov. Den grundläggande distributionen är utformad för att komma igång och utveckla PoC och små pilot. Det ger en strömlinjeformad arkitektur med de lägsta resurser som krävs och en lägre kostnad. Standard distributionen är utformad för att bygga och anpassa en produktions klar lösning och ger en distribution med de nödvändiga elementen för att kunna använda det. För tillförlitlighet och skalning skapas Application-mikrotjänster som Docker-behållare och distribueras med en Orchestrator (Kubernetes som standard). Orchestrator är ansvarig för distribution, skalning och hantering av programmet. Du bör välja ett alternativ baserat på dina aktuella behov. Du kan använda en, samma eller en kombination av båda beroende på ditt projekt steg.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hur gör jag för att konfigurerar du en dynamisk karta på instrument panelen?

Mer information finns i [Uppgradera Map-nyckel för att se enheter på en dynamisk karta](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Var kan jag hitta information om den tidigare versionen av lösningen för fjärrövervakning?

Den tidigare versionen av lösnings acceleratorn för fjärrövervakning kallades IoT Suite den förkonfigurerade lösningen för fjärrövervakning. Du hittar den arkiverade dokumentationen på [https://docs.microsoft.com/previous-versions/azure/iot-suite/](/previous-versions/azure/iot-suite/) .

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösnings acceleratorn för fjärr styrning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsacceleratorn Förutsägande underhåll](./iot-accelerators-predictive-walkthrough.md)
* [Distribuera lösnings Accelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](../iot-fundamentals/iot-security-ground-up.md)