---
title: Gateways för underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Använd Azure IoT Edge för att skapa en transparent, täckande eller proxy gateway-enhet som skickar data från flera underordnade enheter till molnet eller bearbetar den lokalt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cbca0c2509e74a7debf5ba26b361c79b9b208f08
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547123"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Så kan en IoT Edge-enhet användas som gateway

Gatewayer i IoT Edge lösningar ger enhets anslutning och gräns analys till IoT-enheter som annars inte skulle ha dessa funktioner. Azure IoT Edge kan användas för att tillgodose eventuella behov av en IoT-Gateway, oavsett om den är relaterad till anslutning, identitet eller gräns analys. Gateway-mönster i den här artikeln endast referera till egenskaperna för underordnade enhetsanslutning och enhetsidentitet inte hur enhetsdata bearbetas på gatewayen.

## <a name="patterns"></a>Mönster

Det finns tre mönster för att använda en IoT Edge-enhet som en gateway: transparent, protokoll, översättning och översättning av identitet:

* **Transparent** – enheter som teoretiskt sett kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Efterföljande enheter har sina egna IoT Hub-identiteter och använder något av de MQTT-, AMQP- eller HTTP-protokoll. Gatewayen bara skickar kommunikation mellan enheter och IoT Hub. Både enheter och användare som interagerar med dem via IoT Hub är inte medvetna om att en gateway är underställd deras kommunikation. Detta brist på medvetenhet innebär att gatewayen anses vara *transparent*. Referera till [skapa en transparent gateway](how-to-create-transparent-gateway.md) ge specifik information om hur du använder en IoT Edge-enhet som en transparent gateway.
* **Protokollet translation** – även känt som ett mönster för ogenomskinlig gateway, enheter som inte stöder MQTT-, AMQP- eller HTTP kan använda en gateway-enhet för att skicka data till IoT Hub för deras räkning. Gatewayen förstår det protokoll som används av de underordnade enheterna och är den enda enhet som har en identitet i IoT Hub. All information som ser ut som den kommer från en enhet, gatewayen. Efterföljande enheter måste bädda in ytterligare identifieringsinformation i meddelandena om molnprogram vill analysera data på varje enhet. Dessutom primitiver för IoT Hub som twins och metoder är bara tillgängliga för gateway-enheten inte underordnade enheter.
* **Identitet translation** -enheter som inte kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Gatewayen tillhandahåller IoT Hub identitets- och protokollet översättning för de underordnade enheterna. Gatewayen är tillräckligt smarta för att förstå det protokoll som används av underordnade enheter, ge dem identitet och översätta primitiver för IoT Hub. Efterföljande enheter visas i IoT Hub som förstklassig enheter med twins och metoder. En användare kan interagera med enheter i IoT Hub och stöder inte mellanliggande gateway-enheten.

![Diagram - Transparent, protokoll och identitet gateway mönster](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Användningsfall

Alla gateway-mönster ger följande fördelar:

* **Analys vid gränsen** – Använd AI-tjänster lokalt för att bearbeta data som kommer från underordnade enheter utan att skicka full Fidelity telemetri till molnet. Hitta och ta hänsyn till insikter lokalt och skicka bara en delmängd av data till IoT Hub.
* **Underordnad enhet isolering** – gateway-enheten kan skydda alla underordnade enheter att exponeras för internet. Det kan vara mellan en OT-nätverk som inte har någon anslutning och ett IT-nätverk som ger tillgång till Internet.
* **Anslutningen multiplexering** – alla enheter som ansluter till IoT Hub via en IoT Edge gateway använder samma underliggande anslutningen.
* **Trafik smoothing** – The IoT Edge-enhet kommer automatiskt att implementera exponentiell backoff om IoT Hub begränsar trafik, vid spara meddelanden lokalt. Den här förmånen gör din lösning elastiska för trafiktoppar.
* **Offline-support** – gateway-enheten lagrar meddelanden och dubbla uppdateringar som inte kan levereras till IoT Hub.

En gateway som protokollet translation kan också utföra gränsanalyser, enheten isolering, trafik smoothing och offlinestöd till befintliga enheter och nya enheter som är begränsad resurs. Många befintliga enheter som ger upphov till data som kan driva affärsinsikter; men de inte har utvecklats med molnanslutning i åtanke. Täckande gateways gör att dessa data kan låsas upp och användas i en IoT-lösning.

En gateway som gör identitet translation ger dig fördelarna med protokollöversättning och tillåter dessutom för fullständig hantering av underordnade enheter från molnet. Alla enheter i din IoT-lösning som visas i IoT Hub oavsett vilket protokoll som de använder.

## <a name="cheat-sheet"></a>Fusklapp

Här är en snabb fusklapp som jämför IoT Hub primitiver när du använder transparent, täckande (protocol) och proxy-gatewayer.

| &nbsp; | Transparent gateway | Översättning av protokollet | Identitet översättning |
|--------|-------------|--------|--------|
| Identiteter som lagras i IoT Hub-identitetsregistret | Identiteter för alla anslutna enheter | Endast identiteten för gateway-enheten | Identiteter för alla anslutna enheter |
| Enhetstvilling | Varje ansluten enhet har en egen enhetstvilling | Endast gateway har en enhet och modulen twins | Varje ansluten enhet har en egen enhetstvilling |
| Direkta metoder och meddelanden från moln till enhet | Molnet kan lösa varje ansluten enhet individuellt | Molnet kan bara lösa gateway-enheten | Molnet kan lösa varje ansluten enhet individuellt |
| [IoT Hub-begränsningar och kvoter](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Gäller för varje enhet | Gäller för gateway-enheten | Gäller för varje enhet |

När du använder ett mönster för ogenomskinlig gateway (protokollöversättning) kan dela samma kö från moln till enhet, som kan innehålla högst 50 meddelanden i alla enheter som ansluter via denna gateway. Det följer mönstret för ogenomskinlig gateway ska användas när några enheter ansluter via varje fält-gateway, och trafiken moln-till-enhet är låg.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en transparent Gateway:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
