---
title: "Förstå hur en insticksenhet för Azure IoT kan användas som en gateway för andra enheter | Microsoft Docs"
description: "Använda Azure IoT kant för att skapa en transparent täckande eller proxy gateway-enhet som skickar data från flera underordnade enheter till molnet eller bearbetar den lokalt."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3f2f9258b97d4886f41a2b991ff4de7e16379245
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Hur en IoT-enhet kan användas som en gateway - förhandsgranskning

Syftet med gateways i IoT-lösningar är specifik för lösningen och kombinera enhetsanslutning med kant analytics. Azure IoT-kant kan användas för att uppfylla alla behov för en IoT-gateway oavsett om de är relaterade till anslutningen-, identitets- eller edge analytics. Gateway-mönster i den här artikeln endast referera till egenskaperna för underordnade enhetsanslutning och enhetsidentitet, inte hur data på enheten bearbetas på gateway.

## <a name="patterns"></a>Mönster
Det finns tre mönster för att använda en insticksenhet för IoT som gateway: öppet, protokoll, översättning och identitet översättning:
* **Transparent** – enheter som teoretiskt kunde ansluta till IoT-hubb kan ansluta till en gateway-enhet i stället. Detta innebär att de underordnade enheterna har sina egna identiteter för IoT-hubb och använder något av de MQTT, AMQP och HTTP-protokoll. Gatewayen skickar bara kommunikation mellan enheter och IoT-hubb. Enheter är inte medveten om att de kommunicerar med molnet via en gateway och en användare interagerar med enheter i IoT-hubben är medveten om mellanliggande gateway-enheten. Gatewayen är alltså transparent. Referera till den [skapa en transparent gateway] [ lnk-iot-edge-as-transparent-gateway] instruktioner för närmare information om hur du använder en IoT-enhet som en transparent gateway.
* **Protokollet översättning** – enheter som inte stöder MQTT, AMQP och HTTP-använda en gateway-enhet för att skicka data till IoT-hubb. Gatewayen är smart att förstå det protokoll som används av underordnade enheter; Det är emellertid bara enheten som har en identitet i IoT-hubb. All information som ser ut som kommer från en enhet, gatewayen. Detta innebär att efterföljande enheter måste bädda in ytterligare identifieringsinformation i meddelandena om molnprogram vill orsak om data på grundval av per enhet. Dessutom IoT-hubb primitiver som dubbla och metoder är bara tillgängliga för gateway-enheten inte efterföljande enheter.
* **Identitet översättning** -enheter som inte kan ansluta till IoT-hubb som ansluter till en gateway-enhet som tillhandahåller identitets- och översättning för underordnade enheter för IoT-hubb. Gatewayen är smart att förstå det protokoll som används av underordnade enheter, ger dem identitet och översätta IoT-hubb primitiver. Efterföljande enheter visas i IoT-hubb som förstklassigt enheter med twins och metoder. En användare kan interagera med enheter i IoT-hubb och stöder inte mellanliggande gateway-enheten.

![Diagram över gateway mönster][1]

## <a name="use-cases"></a>Användningsfall
Alla gateway-mönster ger följande fördelar:
* **Kant analytics** – Använd AI-tjänster lokalt för att bearbeta data från underordnade enheter utan att skicka fullständig återgivning telemetri till molnet. Hitta och ta hänsyn till insikter lokalt och skicka endast en delmängd av data till IoT-hubb. 
* **Underordnade enheten isolering** – gateway-enheten kan skydda alla underordnade enheter från exponeringen på internet. Det kan bli mellan ett e-nätverk som inte har anslutning och ett IT-nätverk som ger tillgång till Internet. 
* **Anslutningen multiplexering** – alla enheter som ansluter till IoT-hubb via en IoT-kant enheten ska använda samma underliggande anslutningen.
* **Trafik Utjämning** -implementera i IoT-enhet automatiskt exponentiell backoff vid IoT-hubb begränsning vid beständighet meddelanden lokalt. Detta gör din lösning flexibel till toppar i trafiken.
* **Begränsad offlinestöd** - gateway-enheten lagras lokalt meddelanden och dubbla uppdateringar som inte levereras till IoT-hubb.

En gateway har översättning av protokollet kan också utföra edge analytics, enheten isolering, trafik Utjämning och offline-stöd för befintliga enheter och nya enheter som är begränsad resurs. Många befintliga enheter som ger upphov till data som kan ge affärsinsikter; men de inte har utvecklats med molnet anslutningen i åtanke. Täckande gateways kan informationen ska låsas upp och används på en slutpunkt till slutpunkt IoT-lösning.

En gateway som har identitet översättning ger fördelar för översättning av protokollet och tillåter dessutom fullständig hantering av underordnade enheter från molnet. Alla enheter i din IoT-lösningen visas i IoT-hubb oavsett protokoll med de tala.

## <a name="cheat-sheet"></a>Fusklapp
Här är en snabb över blad som jämför IoT-hubb primitiver när du använder transparent, täckande och proxy gateways.

| &nbsp; | Transparent gateway | Översättning av protokollet | Identitet översättning |
|--------|-------------|--------|--------|
| Identiteter som lagras i registret för IoT-hubb-identitet | Identiteten för alla anslutna enheter. | Endast identiteten för gateway-enhet | Identiteten för alla anslutna enheter. |
| Enhetstvilling | Varje enhet har sin egen enhet dubbla | Endast gateway har en enhet och modulen twins | Varje enhet har sin egen enhet dubbla |
| Direkta metoder och meddelanden moln till enhet | Molnet kan adressera varje ansluten enhet individuellt | Molnet kan bara adressera gateway-enhet | Molnet kan adressera varje ansluten enhet individuellt |
| [IoT-hubb begränsningar och kvoter][lnk-iothub-throttles-quotas] | Gäller för varje enhet | Gäller för gateway-enhet | Gäller för varje enhet |

Dela samma kö från moln till enhet, som kan innehålla högst 50 meddelanden när du använder ett mönster för täckande gateway (översättning av protokollet) alla enheter som ansluter via denna gateway. Det följer att täckande gateway mönstret bör endast användas när mycket få enheter ansluter via varje fält gateway och moln till enhet trafiken är låg.

## <a name="next-steps"></a>Nästa steg
Använda en insticksenhet för IoT som en [transparent gateway][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
