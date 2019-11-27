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
ms.openlocfilehash: ec8b6cf61f9fb92f888642d1de7d4d1b9b7ac3df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456653"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Så kan en IoT Edge-enhet användas som gateway

Gatewayer i IoT Edge lösningar ger enhets anslutning och gräns analys till IoT-enheter som annars inte skulle ha dessa funktioner. Azure IoT Edge kan användas för att uppfylla alla behov för en IoT-gateway, oavsett om de är relaterade till anslutningen, identity eller gränsanalyser. Gateway-mönster i den här artikeln endast referera till egenskaperna för underordnade enhetsanslutning och enhetsidentitet inte hur enhetsdata bearbetas på gatewayen.

## <a name="patterns"></a>Mönster

Det finns tre mönster för att använda en IoT Edge-enhet som en gateway: transparent, protokoll, översättning och översättning av identitet:
* **Transparent** – enheter som teoretiskt sett kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Efterföljande enheter har sina egna IoT Hub-identiteter och använder något av de MQTT-, AMQP- eller HTTP-protokoll. Gatewayen bara skickar kommunikation mellan enheter och IoT Hub. Enheterna är inte medvetna om att de kommunicerar med molnet via en gateway, och en användare som interagerar med enheterna i IoT Hub är inte medveten om den mellanliggande gateway-enheten. Gatewayen är därför transparent. Se [skapa en transparent Gateway](how-to-create-transparent-gateway.md) för information om hur du använder en IoT Edge enhet som en transparent Gateway.
* **Protokoll översättning** – även känt som ett ogenomskinligt Gateway-mönster, enheter som inte stöder MQTT, AMQP eller http kan använda en gateway-enhet för att skicka data till IoT Hub för deras räkning. Gatewayen förstår det protokoll som används av de underordnade enheterna och är den enda enhet som har en identitet i IoT Hub. All information som ser ut som den kommer från en enhet, gatewayen. Efterföljande enheter måste bädda in ytterligare identifieringsinformation i meddelandena om molnprogram vill analysera data på varje enhet. Dessutom primitiver för IoT Hub som twins och metoder är bara tillgängliga för gateway-enheten inte underordnade enheter.
* **Identitets översättning** – enheter som inte kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Gatewayen tillhandahåller IoT Hub identitets- och protokollet översättning för de underordnade enheterna. Gatewayen är tillräckligt smarta för att förstå det protokoll som används av underordnade enheter, ge dem identitet och översätta primitiver för IoT Hub. Efterföljande enheter visas i IoT Hub som förstklassig enheter med twins och metoder. En användare kan interagera med enheter i IoT Hub och stöder inte mellanliggande gateway-enheten.

![Diagram - Transparent, protokoll och identitet gateway mönster](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Användningsfall
Alla gateway-mönster ger följande fördelar:
* **Analys vid gränsen** – Använd AI-tjänster lokalt för att bearbeta data som kommer från underordnade enheter utan att skicka full Fidelity telemetri till molnet. Hitta och ta hänsyn till insikter lokalt och skicka bara en delmängd av data till IoT Hub. 
* **Isolering av underordnad enhet** – gateway-enheten kan förhindra att alla underordnade enheter exponeras för Internet. Det kan vara mellan en OT-nätverk som inte har någon anslutning och ett IT-nätverk som ger tillgång till Internet. 
* **Anslutnings-multiplexering** – alla enheter som ansluter till IoT Hub via en IoT Edge Gateway använder samma underliggande anslutning.
* **Trafik utjämning** – IoT Edges enheten implementerar automatiskt exponentiella backoff om IoT Hub begränsar trafik, samtidigt som meddelandena sparas lokalt. Den här förmånen gör din lösning elastiska för trafiktoppar.
* **Offline-support** – gateway-enheten lagrar meddelanden och dubbla uppdateringar som inte kan levereras till IoT Hub.

En gateway som protokollet translation kan också utföra gränsanalyser, enheten isolering, trafik smoothing och offlinestöd till befintliga enheter och nya enheter som är begränsad resurs. Många befintliga enheter som ger upphov till data som kan driva affärsinsikter; men de inte har utvecklats med molnanslutning i åtanke. Täckande gateways gör att dessa data kan låsas upp och användas i en IoT-lösning.

En gateway som gör identitet translation ger dig fördelarna med protokollöversättning och tillåter dessutom för fullständig hantering av underordnade enheter från molnet. Alla enheter i din IoT-lösning som visas i IoT Hub oavsett vilket protokoll som de använder.

## <a name="cheat-sheet"></a>Översiktsblad
Här är en snabb fusklapp som jämför IoT Hub primitiver när du använder transparent, täckande (protocol) och proxy-gatewayer.

| &nbsp; | Transparent gateway | Översättning av protokollet | Identitet översättning |
|--------|-------------|--------|--------|
| Identiteter som lagras i IoT Hub-identitetsregistret | Identiteter för alla anslutna enheter | Endast identiteten för gateway-enheten | Identiteter för alla anslutna enheter |
| Enhetstvilling | Varje ansluten enhet har en egen enhetstvilling | Endast gateway har en enhet och modulen twins | Varje ansluten enhet har en egen enhetstvilling |
| Direkta metoder och meddelanden från moln till enhet | Molnet kan lösa varje ansluten enhet individuellt | Molnet kan bara lösa gateway-enheten | Molnet kan lösa varje ansluten enhet individuellt |
| [IoT Hub begränsningar och kvoter](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Gäller för varje enhet | Gäller för gateway-enheten | Gäller för varje enhet |

När du använder ett mönster för ogenomskinlig gateway (protokollöversättning) kan dela samma kö från moln till enhet, som kan innehålla högst 50 meddelanden i alla enheter som ansluter via denna gateway. Det följer mönstret för ogenomskinlig gateway ska användas när några enheter ansluter via varje fält-gateway, och trafiken moln-till-enhet är låg.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en transparent Gateway: 

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)