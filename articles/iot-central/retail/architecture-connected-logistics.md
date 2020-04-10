---
title: Arkitektur IoT Ansluten logistik | Microsoft-dokument
description: En arkitektur för IoT Connected Logistics-programmall för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 0ecf73443073275bf854d516eedf3286eaadaaa0
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000415"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arkitektur för IoT Central ansluten logistikapplikationsmall



Partner & kund kan använda appmallen & följande vägledning för att utveckla **slut på anslutna logistiklösningar**.

> [!div class="mx-imgBorder"]
> ![instrumentpanel för ansluten logistik](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Uppsättning IoT-taggar som skickar telemetridata till en gateway-enhet
2. Gateway-enheter som skickar telemetri och aggregerade insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan användas för att formatera om dataströmmar och skicka till önskade lagringskonton 
5. Olika affärsarbetsflöden kan drivas av slutanvändarens affärsprogram

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen Telemetriinmatning från IoT-taggar & gateways

## <a name="iot-tags"></a>IoT-taggar
IoT-taggar ger fysiska, omgivande och miljömässiga sensorfunktioner som temperatur, luftfuktighet, stötar, lutning &ljus. IoT-taggar ansluter vanligtvis till gateway-enheten via Zigbee (802.15.4). Taggar är billigare sensorer; Så kan de kasseras i slutet av en typisk logistikresa för att undvika utmaningar med omvänd logistik.

## <a name="gateway"></a>Gateway
Gateways kan också fungera som IoT-taggar med sina omgivande avkänningsfunktioner. Gatewayen möjliggör uppströms Azure IoT-molnanslutning (MQTT) med hjälp av mobila Wi-Fi-kanaler.  WSN-lägena (Wireless Sensor Network) (Wireless Sensor Network) används för nedströmskommunikation med IoT-taggar. Gateways ger på säker molnanslutning, IoT-taggparning, sammanläggning av sensordata, datalagring och möjlighet att konfigurera tröskelvärden för larm.

## <a name="device-management-with-iot-central"></a>Enhetshantering med IoT Central 
Azure IoT Central är en lösningsutvecklingsplattform som förenklar IoT-enhetsanslutning, konfiguration och hantering. Plattformen minskar avsevärt bördan och kostnaderna för IoT-enhetshantering, drift och relaterad utveckling. Kunder & partner kan bygga en end to end-lösningar för att uppnå en digital återkoppling inom logistik.

## <a name="business-insights-and-actions-using-data-egress"></a>Affärsinsikter och åtgärder med hjälp av datarånge 
IoT Central-plattformen ger omfattande utökningsmöjligheter via CDE (Continuous Data Export) och API:er. Affärsinsikter baserade på telemetridatabearbetning eller råtelemetri exporteras vanligtvis till ett önskat affärsprogram. Det kan uppnås med hjälp av webhook, servicebuss, händelsenav eller bloblagring för att skapa, träna och distribuera maskininlärningsmodeller & ytterligare berika insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [distribuerar mall för anslutna lösningar för logistik](./tutorial-iot-central-connected-logistics.md)
* Läs mer om [IoT Central-butiksmallar](./overview-iot-central-retail.md)
* Läs mer om IoT Central se [IoT Central översikt](../core/overview-iot-central.md)
