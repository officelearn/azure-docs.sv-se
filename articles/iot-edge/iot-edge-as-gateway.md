---
title: Gateways för nedströmsenheter – Azure IoT Edge | Microsoft-dokument
description: Använd Azure IoT Edge för att skapa en transparent, ogenomskinlig eller proxygatewayenhet som skickar data från flera underordnade enheter till molnet eller bearbetar den lokalt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cbca0c2509e74a7debf5ba26b361c79b9b208f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547123"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Så kan en IoT Edge-enhet användas som gateway

Gateways i IoT Edge-lösningar ger enhetsanslutning och kantanalys till IoT-enheter som annars inte skulle ha dessa funktioner. Azure IoT Edge kan användas för att tillgodose alla behov av en IoT-gateway, oavsett om den är relaterad till anslutning, identitet eller kantanalys. Gateway-mönster i den här artikeln refererar bara till egenskaper för nedströms enhetsanslutning och enhetsidentitet, inte hur enhetsdata bearbetas på gatewayen.

## <a name="patterns"></a>Mönster

Det finns tre mönster för att använda en IoT Edge-enhet som en gateway: transparent, protokollöversättning och identitetsöversättning:

* **Transparent** – Enheter som teoretiskt kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Nedströmsenheterna har sina egna IoT Hub-identiteter och använder något av MQTT-, AMQP- eller HTTP-protokollen. Gatewayen skickar helt enkelt kommunikation mellan enheterna och IoT Hub. Både enheterna och användarna som interagerar med dem via IoT Hub är omedvetna om att en gateway förmedlar deras kommunikation. Denna brist på medvetenhet innebär att ingången anses *vara transparent.* Se [Skapa en transparent gateway](how-to-create-transparent-gateway.md) för specifika uppgifter om hur du använder en IoT Edge-enhet som en transparent gateway.
* **Protokollöversättning** – Även känt som ett ogenomskinligt gatewaymönster kan enheter som inte stöder MQTT, AMQP eller HTTP använda en gateway-enhet för att skicka data till IoT Hub för deras räkning. Gatewayen förstår det protokoll som används av nedströmsenheterna och är den enda enhet som har en identitet i IoT Hub. All information ser ut som den kommer från en enhet, gatewayen. Underordnade enheter måste bädda in ytterligare identifierande information i sina meddelanden om molnprogram vill analysera data per enhet. Dessutom är IoT Hub-primitiver som tvillingar och metoder endast tillgängliga för gateway-enheten, inte nedströmsenheter.
* **Identitetsöversättning** - Enheter som inte kan ansluta till IoT Hub kan ansluta till en gateway-enhet i stället. Gatewayen tillhandahåller IoT Hub-identitet och protokollöversättning för nedströmsenheterna. Gatewayen är smart nog att förstå det protokoll som används av nedströmsenheterna, ge dem identitet och översätta IoT Hub-primitiver. Nedströmsenheter visas i IoT Hub som förstklassiga enheter med tvillingar och metoder. En användare kan interagera med enheterna i IoT Hub och känner inte till den mellanliggande gateway-enheten.

![Diagram - Transparenta, protokoll och identitetsgatewaymönster](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Användningsfall

Alla gatewaymönster ger följande fördelar:

* **Analytics at the edge** – Använd AI-tjänster lokalt för att bearbeta data som kommer från nedströmsenheter utan att skicka telemetri med full återgivning till molnet. Hitta och reagera på insikter lokalt och skicka bara en delmängd av data till IoT Hub.
* **Nedströms enhetsisolering** – Gateway-enheten kan skydda alla nedströmsenheter från exponering för internet. Den kan sitta mellan ett OT-nätverk som inte har anslutning och ett IT-nätverk som ger åtkomst till webben.
* **Anslutningsmultteknik** - Alla enheter som ansluter till IoT Hub via en IoT Edge-gateway använder samma underliggande anslutning.
* **Trafikutjämning** - IoT Edge-enheten implementerar automatiskt exponentiell backoff om IoT Hub begränsar trafiken, samtidigt som meddelandena sparas lokalt. Den här fördelen gör din lösning motståndskraftig mot toppar i trafiken.
* **Offlinesupport** – Gateway-enheten lagrar meddelanden och dubbla uppdateringar som inte kan levereras till IoT Hub.

En gateway som gör protokollöversättning kan också utföra kantanalys, enhetsisolering, trafikutjämning och offlinestöd till befintliga enheter och nya enheter som är resursbegränsade. Många befintliga enheter producerar data som kan driva affärsinsikter. men de var inte utformade med molnanslutning i åtanke. Ogenomskinliga gateways gör att dessa data kan låsas upp och användas i en IoT-lösning.

En gateway som gör identitetsöversättning ger fördelarna med protokollöversättning och möjliggör dessutom full hanterbarhet för nedströmsenheter från molnet. Alla enheter i din IoT-lösning visas i IoT Hub oavsett vilket protokoll de använder.

## <a name="cheat-sheet"></a>Översiktsblad

Här är en snabb lathund som jämför IoT Hub primitiver när du använder transparenta, ogenomskinliga (protokoll) och proxy gateways.

| &nbsp; | Transparent gateway | Översättning av protokoll | Identitetsöversättning |
|--------|-------------|--------|--------|
| Identiteter som lagras i IoT Hub-identitetsregistret | Identiteter på alla anslutna enheter | Endast gateway-enhetens identitet | Identiteter på alla anslutna enheter |
| Enhetstvilling | Varje ansluten enhet har sin egen enhetstvilling | Endast gatewayen har en enhet och modul tvillingar | Varje ansluten enhet har sin egen enhetstvilling |
| Direkta metoder och meddelanden från molnet till enheten | Molnet kan adressera varje ansluten enhet individuellt | Molnet kan bara adressera gateway-enheten | Molnet kan adressera varje ansluten enhet individuellt |
| [Begränsningar och kvoter för IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Använd på varje enhet | Använda på gateway-enheten | Använd på varje enhet |

När du använder ett ogenomskinligt gateway-mönster (protokollöversättning) delar alla enheter som ansluter via den gatewayen samma moln-till-enhet-kö, som kan innehålla högst 50 meddelanden. Härav följer att det ogenomskinliga gateway-mönstret endast ska användas när få enheter ansluter via varje fältgateway och deras moln-till-enhet-trafik är låg.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en transparent gateway:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
