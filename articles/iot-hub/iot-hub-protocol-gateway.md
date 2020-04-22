---
title: Azure IoT-protokollgateway | Microsoft-dokument
description: Så här använder du en Azure IoT-protokollgateway för att utöka IoT Hub-funktioner och protokollstöd för att göra det möjligt för enheter att ansluta till hubben med protokoll som inte stöds av IoT Hub inbyggt.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: bfd000e2b69f052e25f0ea6cd286b0ca3aef7519
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759718"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Stöd till tilläggsprotokoll för IoT Hub

Azure IoT Hub stöder inbyggt kommunikation över MQTT-, AMQP- och HTTPS-protokollen. I vissa fall kanske enheter eller fältgatewayer inte kan använda något av dessa standardprotokoll och kräver protokollanpassning. I sådana fall kan du använda en anpassad gateway. En anpassad gateway möjliggör protokollanpassning för IoT Hub-slutpunkter genom att överbrygga trafiken till och från IoT Hub. Du kan använda [Azure IoT-protokollgatewayen](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) som en anpassad gateway för att aktivera protokollanpassning för IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT-protokollgateway

Azure IoT-protokollgatewayen är ett ramverk för protokollanpassning som är utformad för dubbelriktad, dubbelriktad enhetskommunikation med IoT Hub. Protokollgatewayen är en direktkomponent som accepterar enhetsanslutningar via ett visst protokoll. Den överbryggar trafiken till IoT Hub över AMQP 1.0.

Du kan distribuera protokollgatewayen i Azure på ett mycket skalbart sätt med hjälp av Azure Service Fabric, Azure Cloud Services-arbetsroller eller Windows virtuella datorer. Dessutom kan protokollgatewayen distribueras i lokala miljöer, till exempel fältgateways.

Azure IoT-protokollgatewayen innehåller ett MQTT-protokollkort som gör att du kan anpassa MQTT-protokollbeteendet om det behövs. Eftersom IoT Hub tillhandahåller inbyggt stöd för MQTT v3.1.1-protokollet bör du bara överväga att använda MQTT-protokollkortet om protokollanpassningar eller specifika krav för ytterligare funktioner krävs.

MQTT-kortet visar också programmeringsmodellen för att skapa protokollkort för andra protokoll. Dessutom kan azure IoT-protokollgatewayprogrammeringsmodellen koppla in anpassade komponenter för specialiserad bearbetning, till exempel anpassad autentisering, meddelandeomvandlingar, komprimering/dekompression eller kryptering/dekryptering av trafik mellan enheterna och IoT Hub.

För flexibilitet finns Azure IoT-protokollgatewayen och MQTT-implementeringen i ett programprojekt med öppen källkod. Du kan använda projektet med öppen källkod för att lägga till stöd för olika protokoll och protokollversioner eller anpassa implementeringen för ditt scenario. 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure IoT-protokollgatewayen och hur du använder och distribuerar den som en del av din IoT-lösning finns i:

* [Azure IoT-protokollgatewaylagringsplats på GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Utvecklarhandboken för Azure IoT-protokollgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Mer information om hur du planerar distributionen av IoT Hub finns i:

* [Jämför med händelsehubbar](iot-hub-compare-event-hubs.md)

* [Skalning, hög tillgänglighet och haveriberedskap](iot-hub-scaling.md)

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)