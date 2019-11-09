---
title: Arkitektur IoT-ansluten logistik | Microsoft Docs
description: En arkitektur för IoT Connected logistik-Programmall för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 797488632f04af9a170f05590154e50310570b28
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890724"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arkitektur för IoT Central ansluten logistik-Programmall

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Partner & kunden kan utnyttja appens mall & följande rikt linjer för att utveckla slut punkt till slut punkt för **anslutna logistik lösningar**.

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Uppsättning IoT-taggar som skickar telemetridata till en gateway-enhet
2. Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan utnyttjas för att formatera om data strömmar och skicka till önskade lagrings konton 
5. Olika affärs arbets flöden kan användas av affärs program för slutanvändare

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen för telemetri från IoT-Taggar & gateways

## <a name="iot-tags"></a>IoT-Taggar
IoT-Taggar tillhandahåller funktioner för fysisk, omgivande och miljö sensor, till exempel temperatur, fuktighet, stöt, lutning & ljus. IoT-Taggar ansluter vanligt vis till en gateway-enhet via ZigBee (802.15.4). Taggar är billiga sensorer; Därför kan de tas bort i slutet av en typisk logistik resa för att undvika utmaningar med omvänd logistik.

## <a name="gateway"></a>Gateway
Gatewayer kan också fungera som IoT-Taggar med deras funktioner för att kunna hantera omgivningen. Gatewayen möjliggör överordnad Azure IoT Cloud-anslutning (MQTT) via mobil nät, Wi-Fi-kanaler.  Lägena Bluetooth, NFC och 802.15.4 för trådlösa sensor nätverk (WSN) används för underordnad kommunikation med IoT-taggar. Gatewayer ger slut punkt till slut punkt för säker moln anslutning, IoT-taggning, sammanslagning av sensor data, datakvarhållning och möjlighet att konfigurera larm tröskelvärden.

## <a name="device-management-with-iot-central"></a>Enhets hantering med IoT Central 
Azure IoT Central är en plattform för lösnings utveckling som gör det enklare att ansluta, konfigurera och hantera IoT-enheter. Plattformen minskar avsevärt belastningen och kostnaderna för IoT-enhetens hantering, åtgärder och relaterad utveckling. Kunder & partner kan skapa ett heltäckande företags lösningar för att få en digital feedback-slinga i logistik.

## <a name="business-insights--actions-via-data-egress"></a>Affärs insikter & åtgärder via utgående data 
IoT Central Platform tillhandahåller omfattande utöknings alternativ via Center (kontinuerlig data export) och API: er. Affärs insikter baserade på telemetri data behandling eller rå telemetri exporteras vanligt vis till ett prioriterat branschspecifika program. Detta kan uppnås via webhook, Service Bus, Event Hub eller Blob Storage för att bygga, träna och distribuera maskin inlärnings modeller & ytterligare förbättra insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig att distribuera en [mall för kopplad logistik lösning](./tutorial-iot-central-connected-logistics-pnp.md)
* Läs mer om [IoT Central mallar för butik](./overview-iot-central-retail-pnp.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../preview/overview-iot-central.md)
