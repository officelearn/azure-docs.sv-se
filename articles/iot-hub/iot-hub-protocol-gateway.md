---
title: Azure IoT-protokollgatewayen | Microsoft Docs
description: Hur du använder en Azure-IoT-protokollgatewayen för att utöka IoT Hub funktioner och Protokollstöd för att aktivera enheter att ansluta till hubben med hjälp av protokoll som inte stöds av IoT Hub automatiskt.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.openlocfilehash: 9dbb7905c2a0fed65ede610577e0fa11a1deef92
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276243"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Stöd för ytterligare protokoll för IoT Hub

Azure IoT Hub har har inbyggt stöd för kommunikation över MQTT-, AMQP- och HTTPS-protokoll. I vissa fall kan kanske enheter eller fält-gateways inte kan använda någon av följande standardprotokoll och kräver protokollet anpassning. I sådana fall kan använda du en anpassad gateway. En anpassad gateway aktiverar protokoll anpassning om IoT Hub-slutpunkter genom bryggning trafik till och från IoT Hub. Du kan använda den [Azure IoT-protokollgatewayen](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) som en anpassad gateway för att aktivera protokollet anpassning för IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT-protokollgatewayen

Azure IoT-protokollgatewayen är ett ramverk för anpassning av protokoll som är utformat för hög skalbarhet, dubbelriktad kommunikation för enheten med IoT Hub. Protokoll-gateway är en direkt komponent som accepterar anslutningar på enheten via ett visst protokoll. De här lösningarna överbryggar trafiken till IoT Hub över AMQP 1.0.

Du kan distribuera protokoll-gateway i Azure på ett mycket skalbart sätt med hjälp av Azure Service Fabric, Azure Cloud Services worker-roller eller Windows-datorer. Protokoll-gateway kan dessutom distribueras i lokala miljöer, till exempel fält-gateways.

Azure IoT-protokollgatewayen innehåller ett MQTT-protokollet kort där du kan anpassa beteendet MQTT-protokollet om det behövs. Eftersom IoT Hub ger inbyggt stöd för protokollet MQTT v3.1.1, bör du endast överväga att använda MQTT-protokollet kortet om protokollet anpassningar eller särskilda krav för ytterligare funktioner krävs.

MQTT-kortet visar också programmeringsmodell för att skapa protokollet nätverkskort för andra protokoll. Azure IoT-protokoll-gateway programmeringsmodell kan dessutom att du installerar anpassade komponenter för särskild bearbetning som anpassad autentisering, meddelande transformationer, komprimering/dekomprimering eller kryptering/dekryptering av trafik mellan enheter och IoT Hub.

För flexibilitet tillhandahålls Azure IoT-protokollgatewayen och MQTT implementering i ett program med öppen källkod-projekt. Du kan använda projekt för öppen källkod för att lägga till stöd för olika protokoll och protokollversioner eller anpassa implementeringen för ditt scenario. 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure IoT-protokollgatewayen och hur du använder och distribuera den som en del av din IoT-lösning finns:

* [Azure IoT-protokoll-gateway-arkivet på GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Azure IoT gateway developer-protokollguide](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Mer information om hur du planerar distributionen av IoT Hub finns:

* [Jämför med Event Hubs](iot-hub-compare-event-hubs.md)

* [Skalning, hög tillgänglighet och katastrofåterställning](iot-hub-scaling.md)

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)