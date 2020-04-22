---
title: Azure IoT Edge och Azure IoT Central | Microsoft-dokument
description: Förstå hur du använder Azure IoT Edge med ett IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 89d068fbd835a5b4f8886a2ed77691f94a701bb0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759100"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Ansluta Azure IoT Edge-enheter till ett Central-program för Azure IoT

*Den här artikeln gäller lösningsbyggare och enhetsutvecklare.*

IoT Edge består av tre komponenter:

* **IoT Edge-moduler** är behållare som kör Azure-tjänster, partnertjänster eller din egen kod. Moduler distribueras till IoT Edge-enheter och körs lokalt på dessa enheter.
* **IoT Edge-körningen** körs på varje IoT Edge-enhet och hanterar de moduler som distribueras till varje enhet.
* Med ett molnbaserat gränssnitt kan du fjärrövervaka och hantera IoT **Edge-enheter.** IoT Central är molngränssnittet.

En **Azure IoT Edge-enhet** kan vara en gateway-enhet, med underordnade enheter som ansluter till IoT Edge-enheten. Den här artikeln delar mer information om underordnade enhetsanslutningsmönster.

En **enhetsmall** definierar funktionerna för enheten och IoT Edge-modulerna. Funktionerna omfattar telemetri som modulen skickar, modulegenskaper och de kommandon som en modul svarar på.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Underordnade enhetsrelationer med en gateway och moduler

Nedströmsenheter kan ansluta till en IoT Edge-gatewayenhet via modulen. `$edgeHub` Den här IoT Edge-enheten blir en transparent gateway i det här scenariot.

![Diagram över transparent gateway](./media/concepts-iot-edge/gateway-transparent.png)

Nedströmsenheter kan också ansluta till en IoT Edge-gatewayenhet via en anpassad modul. I följande scenario ansluter nedströmsenheter via en Anpassad Modbus-modul.

![Diagram över anpassad modulanslutning](./media/concepts-iot-edge/gateway-module.png)

Följande diagram visar anslutning till en IoT Edge gateway-enhet `$edgeHub`genom båda typerna av moduler (anpassade och ).  

![Diagram över anslutning via båda anslutningsmodulerna](./media/concepts-iot-edge/gateway-module-transparent.png)

Slutligen kan nedströmsenheter ansluta till en IoT Edge-gatewayenhet via flera anpassade moduler. Följande diagram visar underordnade enheter som ansluter via en Anpassad Modbus-modul, en anpassad BLE-modul och modulen. `$edgeHub` 

![Diagram över anslutning via flera anpassade moduler](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Distributionsmanifest och enhetsmallar

I IoT Edge kan du distribuera och hantera affärslogik i form av moduler. IoT Edge-moduler är den minsta beräkningsenheten som hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösningsspecifika kod. Mer om hur moduler utvecklas, distribueras och underhålls finns i [IoT Edge-moduler](../../iot-edge/iot-edge-modules.md).

På en hög nivå är ett distributionsmanifest en lista över modultvillingar som är konfigurerade med önskade egenskaper. Ett distributionsmanifest talar om för en IoT Edge-enhet (eller en grupp enheter) vilka moduler som ska installeras och hur de ska konfigureras. Distributionsmanikursar innehåller önskade egenskaper för varje modultvilling. IoT Edge-enheter rapporterar tillbaka de rapporterade egenskaperna för varje modul.

Använd Visual Studio-kod för att skapa ett distributionsmanifest. Mer information finns i [Azure IoT Edge för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

I Azure IoT Central kan du importera ett distributionsmanifest för att skapa en enhetsmall. Följande flödesschema visar en distributionsmanifest livscykel i IoT Central.

![Flödesschema för distribution manifestlivscykeln](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (preview) modellerar en IoT Edge-enhet enligt följande:

* Varje IoT Edge-enhetsmall har en enhetskapacitetsmodell.
* För varje anpassad modul som anges i distributionsmanifestet genereras en modulkapacitetsmodell.
* En relation upprättas mellan varje modulfunktionsmodell och en enhetskapacitetsmodell.
* En modulkapacitetsmodell implementerar modulgränssnitt.
* Varje modulgränssnitt innehåller telemetri, egenskaper och kommandon.

![Diagram över IoT Edge-modellering](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge-gatewayenheter

Om du har valt en IoT Edge-enhet som en gateway-enhet kan du lägga till underordnade relationer till enhetskapacitetsmodeller för enheter som du vill ansluta till gateway-enheten.

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare är ett förslag på nästa steg att lära dig mer om [gateway-enhetstyper i IoT Central](./tutorial-define-gateway-device-type.md).
