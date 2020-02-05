---
title: Azure IoT Edge och Azure IoT Central | Microsoft Docs
description: Förstå hur du använder Azure IoT Edge med ett IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027074"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Anslut Azure IoT Edge enheter till ett Azure IoT Central-program

IoT Edge består av tre komponenter:

* **IoT Edge moduler** är behållare som kör Azure-tjänster, partner tjänster eller din egen kod. Moduler distribueras till IoT Edge enheter och körs lokalt på dessa enheter.
* **IoT Edge runtime** körs på varje IoT Edge enhet och hanterar de moduler som distribueras till varje enhet.
* Med ett **moln baserat gränssnitt** kan du fjärrövervaka och hantera IoT Edge enheter. IoT Central är moln gränssnittet.

En **Azure IoT Edge** enhet kan vara en gateway-enhet, med underordnade enheter som ansluter till IoT Edge enheten. Den här artikeln delar mer information om underordnade enhets anslutnings mönster.

En **enhets mall** definierar funktionerna i enheten och IoT Edge moduler. Funktionerna omfattar telemetri som modulen skickar, modul egenskaper och de kommandon som en modul svarar på.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Underordnade enhets relationer med en gateway och moduler

Underordnade enheter kan ansluta till en IoT Edge gateway-enhet via modulen `$edgeHub`. Den här IoT Edge enheten blir en transparent gateway i det här scenariot.

![Diagram över transparent Gateway](./media/concepts-iot-edge/gateway-transparent.png)

Underordnade enheter kan också ansluta till en IoT Edge gateway-enhet via en anpassad modul. I följande scenario ansluter underordnade enheter till en anpassad Modbus-modul.

![Diagram över anslutning till anpassad modul](./media/concepts-iot-edge/gateway-module.png)

Följande diagram visar anslutning till en IoT Edge-gatewayenhet via båda typerna av moduler (anpassade och `$edgeHub`).  

![Diagram över anslutning via båda nätmodulerna](./media/concepts-iot-edge/gateway-module-transparent.png)

Slutligen kan underordnade enheter ansluta till en IoT Edge gateway-enhet via flera anpassade moduler. Följande diagram visar underordnade enheter som ansluter via en anpassad Modbus-modul, en modul för anpassad tabell och `$edgeHub`-modulen. 

![Diagram över anslutning via flera anpassade moduler](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Distributions manifest och enhetsspecifika

I IoT Edge kan du distribuera och hantera affärs logik i form av moduler. IoT Edge moduler är den minsta beräknings enheten som hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösnings specifika kod. Information om hur moduler utvecklas, distribueras och underhålls finns i [IoT Edge moduler](../../iot-edge/iot-edge-modules.md).

På en hög nivå är en lista över modultvillingar som är konfigurerade med deras önskade egenskaper i ett manifest för distribution. Ett distributions manifest talar om för en IoT Edge enhet (eller en grupp av enheter) vilka moduler som ska installeras och hur du konfigurerar dem. Distributions manifest innehåller de önskade egenskaperna för varje modul med dubbla. IoT Edge enheterna rapporterar de rapporterade egenskaperna för varje modul.

Använd Visual Studio Code för att skapa ett distributions manifest. Mer information finns i [Azure IoT Edge för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

I Azure IoT Central kan du importera ett distributions manifest för att skapa en enhets mall. Följande flödes schema visar livs cykeln för distributions manifest i IoT Central.

![Flödes schema för distributions Manifestets livs cykel](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (för hands version) modellerar en IoT Edge-enhet på följande sätt:

* Varje IoT Edge enhets mal len har en enhets kapacitets modell.
* För varje anpassad modul som anges i distributions manifestet genereras en moduls funktions modell.
* En relation upprättas mellan varje moduls funktions modell och en enhets kapacitets modell.
* En modul kapacitets modell implementerar modulens gränssnitt.
* Varje modul gränssnitt innehåller telemetri, egenskaper och kommandon.

![Diagram över IoT Edge modellering](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge gateway-enheter

Om du har valt en IoT Edge enhet som en gateway-enhet kan du lägga till underordnade relationer till enhets kapacitets modeller för enheter som du vill ansluta till gateway-enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du vet vad IoT Central programmallar är, kom igång genom att [skapa ett IoT Central program](quick-deploy-iot-central.md).