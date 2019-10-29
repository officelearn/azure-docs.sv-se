---
title: Arkitektur IoT Central Digital distributions Center | Microsoft Docs
description: En arkitektur för program mal len för digital distributions Center för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 1ce0896d3b9fd60190625b49d0e4580a2038e638
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027379"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arkitektur för IoT Central app-mall för digital distributions Center

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Partner & kunden kan använda appens mall & följande rikt linjer för att utveckla lösningar för att utveckla slut punkt till slut punkt för **digital distribution Center** .

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
2. Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan utnyttjas för att formatera om data strömmar och skicka till önskade lagrings konton 
5. Bearbetade data lagras i snabb lagring för nära real tids åtgärder eller kall lagring för ytterligare Insight-förbättringar baserat på ML-eller batch-analys. 
6. Logic Apps kan användas för att driva olika arbets flöden i affärs program för slutanvändare

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen

## <a name="video-cameras"></a>Video kameror 
Video kameror är de viktigaste sensorerna i detta digitalt anslutna eko system i företags skala. Framsteg i maskin inlärning och artificiell intelligens som gör att video kan omvandlas till strukturerade data och bearbeta den på gränsen innan den skickas till molnet. Vi kan använda IP-kameror för att avbilda bilder, komprimera dem i kameran och sedan skicka den komprimerade data över Edge Compute för video analys pipelinen eller använda GigE vision-kameror för att avbilda bilder på sensorn och sedan skicka avbildningarna direkt till Azure IoT Edge , som sedan komprimeras före bearbetning i Video Analytics-pipeline. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
"Kameror-som-sensorer" och Edge-arbetsbelastningar hanteras lokalt av Azure IoT Edge och kamera strömmen bearbetas av Analytics pipeline. Pipeline för video analys i Azure IoT Edge ger flera fördelar, inklusive minskad svars tid, förbrukning av låg bandbredd som resulterar i låg latens för snabb data bearbetning. Endast de viktigaste metadata, insikter eller åtgärder skickas till molnet för ytterligare åtgärder eller undersökning. 

## <a name="device-management-with-iot-central"></a>Enhets hantering med IoT Central 
Azure IoT Central är en plattform för lösnings utveckling som fören klar IoT-enheten & Azure IoT Edge Gateway-anslutning, konfiguration och hantering. Plattformen minskar avsevärt belastningen och kostnaderna för IoT-enhetens hantering, åtgärder och relaterad utveckling. Kunder & partner kan skapa en slut punkt för företags lösningar för att få en digital feedback-slinga i distributions Center.

## <a name="business-insights--actions-via-data-egress"></a>Affärs insikter & åtgärder via utgående data 
IoT Central Platform tillhandahåller omfattande utöknings alternativ via Center (kontinuerlig data export) och API: er. Affärs insikter baserade på telemetri data behandling eller rå telemetri exporteras vanligt vis till ett prioriterat branschspecifika program. Detta kan uppnås via webhook, Service Bus, Event Hub eller Blob Storage för att bygga, träna och distribuera maskin inlärnings modeller & ytterligare förbättra insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du distribuerar [mallen för digital distributions Center](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Läs mer om [IoT Central mallar för butik](./overview-iot-central-retail-pnp.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central-pnp.md)
