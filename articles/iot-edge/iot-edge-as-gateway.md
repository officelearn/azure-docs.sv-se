---
title: Gatewayer för underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Använd Azure IoT Edge för att skapa en transparent, ogenomskinlig eller proxy-gatewayenhet som skickar data från flera efterföljande enheter till molnet eller bearbetar dem lokalt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 916eeaa60bc054301af039164ce1c14e77ceb91a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733520"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Så kan en IoT Edge-enhet användas som gateway

Gatewayer i IoT Edge lösningar ger enhets anslutning och gräns analys till IoT-enheter som annars inte skulle ha dessa funktioner. Azure IoT Edge kan användas för att tillgodose eventuella behov av en IoT-Gateway, oavsett om den är relaterad till anslutning, identitet eller gräns analys. Gateway-mönster i den här artikeln avser endast egenskaper för underordnad enhets anslutning och enhets identitet, inte hur enhets data bearbetas på gatewayen.

## <a name="patterns"></a>Mönster

Det finns tre mönster för att använda en IoT Edge enhet som en gateway: transparent, protokoll översättning och identitets översättning:

* **Transparent** – enheter som teoretiskt sett kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. De underordnade enheterna har sina egna IoT Hub identiteter och använder något av MQTT-, AMQP-eller HTTP-protokollen. Gatewayen skickar bara kommunikation mellan enheterna och IoT Hub. Både enheter och användare som interagerar med dem via IoT Hub är inte medvetna om att en gateway är underställd deras kommunikation. Detta brist på medvetenhet innebär att gatewayen anses vara *transparent*. Se [skapa en transparent Gateway](how-to-create-transparent-gateway.md) för information om hur du använder en IoT Edge enhet som en transparent Gateway.
* **Protokoll översättning** – även känt som ett ogenomskinligt Gateway-mönster, enheter som inte stöder MQTT, AMQP eller http kan använda en gateway-enhet för att skicka data till IoT Hub för deras räkning. Gatewayen förstår det protokoll som används av de underordnade enheterna och är den enda enhet som har en identitet i IoT Hub. All information ser ut som om den kommer från en enhet, gatewayen. Underordnade enheter måste bädda in ytterligare identifierings information i sina meddelanden om moln program vill analysera data per enhet. Dessutom är IoT Hub primitiver som dubbla och metoder bara tillgängliga för gateway-enheten, inte underordnade enheter.
* **Identitets översättning** – enheter som inte kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Gatewayen tillhandahåller IoT Hub identitets-och protokoll översättning för de efterföljande enheterna. Gatewayen är tillräckligt smart för att förstå det protokoll som används av de underordnade enheterna, ge dem identitet och översätta IoT Hub primitiver. Underordnade enheter visas i IoT Hub som enheter med en första klass med dubbla och metoder. En användare kan interagera med enheterna i IoT Hub och är inte medvetna om den mellanliggande gateway-enheten.

![Diagram – transparenta, protokoll och identitets-Gateway-mönster](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Användningsfall

Alla gateway-mönster ger följande fördelar:

* **Analys vid gränsen** – Använd AI-tjänster lokalt för att bearbeta data som kommer från underordnade enheter utan att skicka full Fidelity telemetri till molnet. Hitta och reagera på insikter lokalt och skicka endast en delmängd data till IoT Hub.
* **Isolering av underordnad enhet** – gateway-enheten kan förhindra att alla underordnade enheter exponeras för Internet. Den kan sitta mellan ett nätverks nätverk som inte har någon anslutning och ett IT-nätverk som ger åtkomst till webben.
* **Anslutnings-multiplexering** – alla enheter som ansluter till IoT Hub via en IoT Edge Gateway använder samma underliggande anslutning.
* **Trafik utjämning** – IoT Edges enheten implementerar automatiskt exponentiella backoff om IoT Hub begränsar trafik, samtidigt som meddelandena sparas lokalt. Den här förmånen gör din lösning flexibel till toppar i trafik.
* **Offline-support** – gateway-enheten lagrar meddelanden och dubbla uppdateringar som inte kan levereras till IoT Hub.

En gateway som har protokoll översättning kan också utföra gräns analys, enhets isolering, trafik utjämning och offline-stöd till befintliga enheter och nya enheter som är resurs begränsade. Många befintliga enheter producerar data som kan leda till affärs insikter. de har dock inte utformats med moln anslutning i åtanke. Täckande gateways gör att dessa data kan låsas upp och användas i en IoT-lösning.

En gateway som har identitets översättning tillhandahåller fördelarna med protokoll översättning och ger också fullständig hanterbarhet av underordnade enheter från molnet. Alla enheter i din IoT-lösning visas i IoT Hub oavsett vilket protokoll de använder.

## <a name="cheat-sheet"></a>Översiktsblad

Här är ett snabbt lathund-blad som jämför IoT Hub primitiver när du använder transparent, ogenomskinlig (protokoll) och proxy-gatewayer.

| &nbsp; | Transparent Gateway | Protokoll Översättning | Identitets Översättning |
|--------|-------------|--------|--------|
| Identiteter lagrade i IoT Hub identitets registret | Identiteter för alla anslutna enheter | Endast gateway-enhetens identitet | Identiteter för alla anslutna enheter |
| Enhetstvilling | Varje ansluten enhet har sin egen enhet | Endast gatewayen har en enhet och modul är dubbla | Varje ansluten enhet har sin egen enhet |
| Direkta metoder och meddelanden från moln till enhet | Molnet kan adressera varje ansluten enhet individuellt | Molnet kan bara adressera gateway-enheten | Molnet kan adressera varje ansluten enhet individuellt |
| [IoT Hub begränsningar och kvoter](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Använd för varje enhet | Tillämpa på gateway-enheten | Använd för varje enhet |

När du använder ett mönster för täckande Gateway (protokoll översättning) delar alla enheter som ansluter via den gatewayen samma moln-till-enhet-kö, som kan innehålla högst 50 meddelanden. Det följer med att mönstret för täckande Gateway endast ska användas när få enheter ansluter via varje fält-gateway och deras trafik från moln till enhet är låg.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en transparent Gateway:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
