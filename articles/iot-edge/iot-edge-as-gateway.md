---
title: Gateways för underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Använd Azure IoT Edge för att skapa en transparent, täckande eller proxy gateway-enhet som skickar data från flera underordnade enheter till molnet eller bearbetar den lokalt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6e54d28d494a58966ea5312492dc79ced4c50a8d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092289"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Hur en IoT Edge-enhet kan användas som en gateway

Gateways i IoT-lösningar ger enhetsanslutning och gränsanalyser IoT-enheter som annars inte skulle ha dessa funktioner. Azure IoT Edge kan användas för att uppfylla alla behov för en IoT-gateway, oavsett om de är relaterade till anslutningen, identity eller gränsanalyser. Gateway-mönster i den här artikeln endast referera till egenskaperna för underordnade enhetsanslutning och enhetsidentitet inte hur enhetsdata bearbetas på gatewayen.

## <a name="patterns"></a>Mönster

Det finns tre mönster för att använda en IoT Edge-enhet som en gateway: transparent, protokoll, översättning och översättning av identitet:
* **Transparent** – enheter som teoretiskt sett kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Efterföljande enheter har sina egna IoT Hub-identiteter och använder något av de MQTT-, AMQP- eller HTTP-protokoll. Gatewayen bara skickar kommunikation mellan enheter och IoT Hub. Enheterna är inte medveten om att de kommunicerar med molnet via en gateway och en användare interagerar med enheter i IoT Hub är medveten om mellanliggande gateway-enheten. Gatewayen är därför transparent. Referera till [skapa en transparent gateway](how-to-create-transparent-gateway.md) ge specifik information om hur du använder en IoT Edge-enhet som en transparent gateway.
* **Protokollet translation** – även känt som ett mönster för ogenomskinlig gateway, enheter som inte stöder MQTT-, AMQP- eller HTTP kan använda en gateway-enhet för att skicka data till IoT Hub för deras räkning. Gatewayen kan tolka protokollet som används av efterföljande enheter; Det är den enda enhet som har identitet i IoT Hub. All information som ser ut som den kommer från en enhet, gatewayen. Efterföljande enheter måste bädda in ytterligare identifieringsinformation i meddelandena om molnprogram vill analysera data på varje enhet. Dessutom primitiver för IoT Hub som twins och metoder är bara tillgängliga för gateway-enheten inte underordnade enheter.
* **Identitet translation** -enheter som inte kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Gatewayen tillhandahåller IoT Hub identitets- och protokollet översättning för de underordnade enheterna. Gatewayen är tillräckligt smarta för att förstå det protokoll som används av underordnade enheter, ge dem identitet och översätta primitiver för IoT Hub. Efterföljande enheter visas i IoT Hub som förstklassig enheter med twins och metoder. En användare kan interagera med enheter i IoT Hub och stöder inte mellanliggande gateway-enheten.

![Diagram - Transparent, protokoll och identitet gateway mönster](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Användningsfall
Alla gateway-mönster ger följande fördelar:
* **Edge-analytics** – Använd AI-tjänster lokalt för att bearbeta data som kommer från underordnade enheter utan att skicka fullständig exakthet telemetri till molnet. Hitta och ta hänsyn till insikter lokalt och skicka bara en delmängd av data till IoT Hub. 
* **Underordnad enhet isolering** – gateway-enheten kan skydda alla underordnade enheter att exponeras för internet. Det kan vara mellan en OT-nätverk som inte har någon anslutning och ett IT-nätverk som ger tillgång till Internet. 
* **Anslutningen multiplexering** – alla enheter som ansluter till IoT Hub via en IoT Edge gateway använder samma underliggande anslutningen.
* **Trafik smoothing** – The IoT Edge-enhet kommer automatiskt att implementera exponentiell backoff om IoT Hub begränsar trafik, vid spara meddelanden lokalt. Den här förmånen gör din lösning elastiska för trafiktoppar.
* **Begränsat stöd för offline** – gateway-enheten lagrar meddelanden och twin uppdateringar som inte kan levereras till IoT Hub.

En gateway som protokollet translation kan också utföra gränsanalyser, enheten isolering, trafik smoothing och offlinestöd till befintliga enheter och nya enheter som är begränsad resurs. Många befintliga enheter som ger upphov till data som kan driva affärsinsikter; men de inte har utvecklats med molnanslutning i åtanke. Täckande gatewayer kan dessa data för att låsas upp och används i en IoT-lösning från slutpunkt till slutpunkt.

En gateway som gör identitet translation ger dig fördelarna med protokollöversättning och tillåter dessutom för fullständig hantering av underordnade enheter från molnet. Alla enheter i din IoT-lösning som visas i IoT Hub oavsett vilket protokoll som de använder.

## <a name="cheat-sheet"></a>Översiktsblad
Här är en snabb fusklapp som jämför IoT Hub primitiver när du använder transparent, täckande (protocol) och proxy-gatewayer.

| &nbsp; | Transparent gateway | Översättning av protokollet | Identitet översättning |
|--------|-------------|--------|--------|
| Identiteter som lagras i IoT Hub-identitetsregistret | Identiteter för alla anslutna enheter | Endast identiteten för gateway-enheten | Identiteter för alla anslutna enheter |
| Enhetstvilling | Varje ansluten enhet har en egen enhetstvilling | Endast gateway har en enhet och modulen twins | Varje ansluten enhet har en egen enhetstvilling |
| Direkta metoder och meddelanden från moln till enhet | Molnet kan lösa varje ansluten enhet individuellt | Molnet kan bara lösa gateway-enheten | Molnet kan lösa varje ansluten enhet individuellt |
| [IoT Hub-begränsningar och kvoter](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Gäller för varje enhet | Gäller för gateway-enheten | Gäller för varje enhet |

När du använder ett mönster för ogenomskinlig gateway (protokollöversättning) kan dela samma kö från moln till enhet, som kan innehålla högst 50 meddelanden i alla enheter som ansluter via denna gateway. Det följer mönstret för ogenomskinlig gateway ska användas när några enheter ansluter via varje fält-gateway, och trafiken moln-till-enhet är låg.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du konfigurerar en IoT Edge-enhet som en [transparent gateway](how-to-create-transparent-gateway-linux.md).
