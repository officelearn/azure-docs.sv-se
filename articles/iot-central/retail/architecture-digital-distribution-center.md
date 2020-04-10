---
title: Arkitektur IoT Central Digital Distribution Center | Microsoft-dokument
description: En arkitektur för applikationsmallen Digital Distribution Center för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: b73d065b43aff5f9793e642a102ff8bb2a7be036
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000450"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arkitektur för IoT Central digital distribution center applikationsmall



Partners och kunder kan använda appmallen & följande vägledning för att utveckla lösningar för **digitala distributionscenter** från slutpunkt till slutpunkt.

> [!div class="mx-imgBorder"]
> ![digitalt distributionscenter](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
2. Gateway-enheter som skickar telemetri och aggregerade insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan användas för att formatera om dataströmmar och skicka till önskade lagringskonton 
5. Bearbetade data lagras i frekvent lagring för åtgärder i nära realtid eller kalllagring för ytterligare insiktsförbättringar som baseras på ML- eller batchanalys. 
6. Logic Apps kan användas för att driva olika affärsarbetsflöden i slutanvändarens affärsprogram

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen

## <a name="video-cameras"></a>Videokameror 
Videokameror är de primära sensorerna i detta digitalt anslutna ekosystem i företagsskala. Framsteg inom maskininlärning och artificiell intelligens som gör att video kan omvandlas till strukturerade data och bearbeta den vid kanten innan den skickas till molnet. Vi kan använda IP-kameror för att ta bilder, komprimera dem på kameran och sedan skicka komprimerade data över kantberäkningen för videoanalyspipelline eller använda GigE vision-kameror för att ta bilder på sensorn och sedan skicka dessa bilder direkt till Azure IoT Edge, som sedan komprimerar innan du bearbetar i videoanalyspipeline. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-gateway
"Kameror som sensorer" och kantarbetsbelastningar hanteras lokalt av Azure IoT Edge och kameraströmmen bearbetas av analyspipeline. Pipelinen för videoanalysbearbetning på Azure IoT Edge ger många fördelar, inklusive minskad svarstid, låg bandbreddsförbrukning, vilket resulterar i låg svarstid för snabb databearbetning. Endast de viktigaste metadata, insikter eller åtgärder skickas till molnet för ytterligare åtgärder eller undersökning. 

## <a name="device-management-with-iot-central"></a>Enhetshantering med IoT Central 
Azure IoT Central är en lösningsutvecklingsplattform som förenklar IoT-enhet & Azure IoT Edge gateway-anslutning, konfiguration och hantering. Plattformen minskar avsevärt bördan och kostnaderna för IoT-enhetshantering, drift och relaterad utveckling. Kunder & partner kan skapa en end to end-lösningar för att uppnå en digital återkoppling i distributionscentraler.

## <a name="business-insights-and-actions-using-data-egress"></a>Affärsinsikter och åtgärder med hjälp av datarånge 
IoT Central-plattformen ger omfattande utökningsmöjligheter via CDE (Continuous Data Export) och API:er. Affärsinsikter som baseras på telemetridatabearbetning eller råtelemetri exporteras vanligtvis till ett önskat branschprogram. Det kan uppnås genom webhook, Service Bus, event hub, eller blob lagring för att bygga, träna och distribuera maskininlärningsmodeller och ytterligare berika insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du distribuerar [mall för digitala distributionscenter](./tutorial-iot-central-digital-distribution-center.md)
* Läs mer om [IoT Central-butiksmallar](./overview-iot-central-retail.md)
* Läs mer om IoT Central se [IoT Central översikt](../core/overview-iot-central.md)
