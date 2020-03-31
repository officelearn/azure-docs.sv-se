---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187462"
---
## <a name="automatic-device-management"></a>Automatisk enhetshantering
Automatisk enhetshantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna att hantera stora enhetsflottor under hela deras livscykler. Med Automatisk enhetshantering kan du rikta in dig på en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de kommer in i omfånget.  Består av [automatiska enhetskonfigurationer](../articles/iot-hub/iot-hub-auto-device-config.md) och [IoT Edge automatiska distributioner](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge möjliggör molndriven distribution av Azure-tjänster och lösningsspecifik kod till lokala enheter. IoT Edge-enheter kan aggregera data från andra enheter för att utföra databehandling och analys innan data skickas till molnet. Mer information finns i [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>IoT Edge-agent
Den del av IoT Edge-körningen som ansvarar för distribution och övervakning av moduler.

## <a name="iot-edge-device"></a>IoT Edge-enhet
IoT Edge-enheter har IoT Edge-körningen installerad och flaggas som **IoT Edge-enhet** i enhetsinformationen. Lär dig hur du [distribuerar Azure IoT Edge på en simulerad enhet i Linux - preview](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Automatisk distribution av IoT Edge
En automatisk distribution i IoT Edge konfigurerar en måluppsättning IoT Edge-enheter för att köra en uppsättning IoT Edge-moduler. Varje distribution säkerställer kontinuerligt att alla enheter som matchar dess målvillkor kör den angivna uppsättningen moduler, även när nya enheter skapas eller ändras för att matcha målvillkoret. Varje IoT Edge-enhet får endast distributionen med högst prioritet vars målvillkor den uppfyller. Läs mer om [automatisk distribution av IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge-distributionsmanifest
Ett Json-dokument som innehåller den information som ska kopieras i en eller flera IoT Edge-enheters moduldvillingar för att distribuera en uppsättning moduler, vägar och tillhörande modul önskade egenskaper.

## <a name="iot-edge-gateway-device"></a>IoT Edge-gatewayenhet
En IoT Edge-enhet med nedströmsenhet. Nedströmsenheten kan vara antingen IoT Edge eller inte IoT Edge-enhet.

## <a name="iot-edge-hub"></a>IoT Edge-nav
Den del av IoT Edge-körningen som ansvarar för modulkommunikation till modulkommunikation, uppströms (mot IoT Hub) och nedströms (bort från IoT Hub) kommunikation. 

## <a name="iot-edge-leaf-device"></a>IoT-bladenhet
En IoT Edge-enhet utan nedströmsenhet. 

## <a name="iot-edge-module"></a>IoT Edge-modul
En IoT Edge-modul är en Docker-behållare som du kan distribuera till IoT Edge-enheter. Den utför en viss uppgift, till exempel att inta ett meddelande från en enhet, omvandla ett meddelande eller skicka ett meddelande till en IoT-hubb. Den kommunicerar med andra moduler och skickar data till IoT Edge-körningen. [Förstå kraven och verktygen för att utveckla IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT Edge-modulidentitet
En post i IoT Hub-modulens identitetsregister som beskriver förekomsten och säkerhetsautentiseringsuppgifterna som ska användas av en modul för att autentisera med ett kantnav eller IoT Hub.

## <a name="iot-edge-module-image"></a>Bild av IoT Edge-modul
Docker-avbildningen som används av IoT Edge-körningen för att instansiera modulinstanser.

## <a name="iot-edge-module-twin"></a>Tvilling av IoT Edge-modul
Ett Json-dokument sparades i IoT Hub som lagrar tillståndsinformationen för en modulinstans.

## <a name="iot-edge-priority"></a>IoT Edge-prioritet
När två IoT Edge-distributioner riktar sig till samma enhet tillämpas distributionen med högre prioritet. Om två distributioner har samma prioritet tillämpas distributionen med det senare skapandedatumet. Läs mer om [prioritet](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-körning
IoT Edge-körningen innehåller allt som Microsoft distribuerar för att installeras på en IoT Edge-enhet. Den innehåller Edge-agent, Edge-hubb och säkerhetsdemonen IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge-uppsättning moduler till en enda enhet
En åtgärd som kopierar innehållet i ett IoT Edge-manifest på en enhets modultvilling. Det underliggande API:et är en allmän "apply configuration", som helt enkelt tar ett IoT Edge-manifest som indata.

## <a name="iot-edge-target-condition"></a>Villkor för IoT Edge-mål
I en IoT Edge-distribution är målvillkor alla booleska villkor på enhetstvillingars taggar för att välja målenheter för distributionen, till exempel **tag.environment = prod**. Målvillkoret utvärderas kontinuerligt så att det omfattar alla nya enheter som uppfyller kraven eller tar bort enheter som inte längre gör det. Läs mer om [målvillkor](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)