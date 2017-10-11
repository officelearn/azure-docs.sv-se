---
title: Azure IoT-protokollet gateway | Microsoft Docs
description: "Hur du använder en gateway för Azure IoT-protokollet för att utöka IoT-hubb funktioner och stöd för protokollet för att aktivera enheter att ansluta till hubben med hjälp av protokoll som inte stöds av IoT-hubb internt."
services: iot-hub
documentationcenter: 
author: kdotchkoff
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: kdotchko
ms.openlocfilehash: b2ad2c6f5eeec2f803e2d2f5f98831b551efa8b1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# Stöd för fler protokoll för IoT-hubb
Azure IoT-hubb har inbyggt stöd för kommunikation över MQTT, AMQP och HTTP-protokoll. I vissa fall kan enheter eller gateways för fältet kanske inte kan använda någon av dessa standardprotokoll och kräver protokollet anpassning. I sådana fall måste använda du en anpassad gateway. En anpassad gateway kan aktivera protokollet anpassning för IoT-hubb-slutpunkter med bryggning trafik till och från IoT-hubb. Du kan använda den [Azure IoT-protokollet gateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) som en anpassad gateway för att aktivera protokollet anpassning för IoT-hubb.

## Azure IoT protocol-gateway
Azure IoT protocol-gateway är ett ramverk för protokollet anpassning som är avsedd för hög skalning, enheten dubbelriktad kommunikation med IoT-hubben. Protocol-gateway är en direkt komponent som accepterar enhetsanslutningar över ett visst protokoll. Det få trafiken till IoT-hubb via AMQP 1.0. Azure IoT-protokollgatewayen är tillgänglig som en öppen källkod projekt som ger flexibilitet för att lägga till stöd för olika protokoll och protokoll version.

Du kan distribuera protocol-gateway i Azure på ett mycket skalbart sätt med hjälp av Azure Service Fabric, arbetsroller i Azure Cloud Services och Windows-datorer. Dessutom kan protocol-gateway distribueras i lokala miljöer, t.ex fältet gateways.

Azure IoT-protokollet gateway innehåller en MQTT protocol-kortet som gör att du kan anpassa beteendet MQTT protokoll om det behövs. Eftersom IoT-hubben har inbyggt stöd för protokollet MQTT v3.1.1, bör endast du använda protocol-kortet MQTT om protokollet anpassningar eller särskilda krav för ytterligare funktioner krävs.

MQTT kortet visas också programmeringsmodell för att skapa protokollet nätverkskort för andra protokoll. Dessutom kan Azure IoT-protokollet gateway programmeringsmodell du ansluta i anpassade komponenter för särskild bearbetning som anpassad autentisering, meddelande omvandlingar, komprimering/dekomprimering eller kryptering och dekryptering av trafik mellan enheter och IoT-hubb.

För flexibilitet finns protocol-gateway och MQTT implementering i ett projekt med öppen källkod. På så sätt kan du anpassa implementeringen efter behov.

## Nästa steg
Mer information om Azure IoT protocol-gateway och hur du använder och distribuera den som en del av IoT-lösningen finns:

* [Azure IoT-protokollet gateway-databasen på GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT-protokollet gateway utvecklarhandboken](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Mer information om hur du planerar distributionen av IoT-hubb finns:

* [Jämför med Händelsehubbar][lnk-compare]
* [Skalning, hög tillgänglighet och Katastrofåterställning][lnk-scaling]
* [Utvecklarhandbok för IoT-hubb][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
