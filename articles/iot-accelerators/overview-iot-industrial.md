---
title: Översikt över Azure industrial IoT | Microsoft-dokument
description: Den här artikeln innehåller en översikt över industriell IoT. Det förklarar den anslutna fabriken, fabriksgolv anslutning och säkerhetskomponenter i IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73828130"
---
# <a name="what-is-industrial-iot-iiot"></a>Vad är industriell IoT (IIoT)

IIoT är det industriella sakernas internet. IIoT ökar den industriella effektiviteten genom tillämpning av IoT inom tillverkningsindustrin. 

## <a name="improve-industrial-efficiencies"></a>Förbättra den industriella effektiviteten

Förbättra din operativa produktivitet och lönsamhet med en ansluten fabrikslösningsaccelerator. Anslut och övervaka din industriella utrustning och dina enheter i molnet – inklusive dina maskiner som redan arbetar på fabriksgolvet. Analysera dina IoT-data för insikter som hjälper dig att öka prestandan på hela fabriksgolvet.

Minska den tidskrävande processen med att komma åt maskiner på fabriksgolvet med OPC Twin och fokusera din tid på att bygga IIoT-lösningar. Effektivisera certifikathantering och industriell tillgångsintegrering med OPC Vault och känna dig säker på att tillgångsanslutningen är säkrad. Dessa mikrotjänster ger ett REST-liknande API ovanpå [Azure Industrial IoT-komponenter](https://github.com/Azure/azure-iiot-opc-ua). Tjänst-API:et ger dig kontroll över edge-modulfunktioner. 

![Översikt över branschanpassat IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Mer information om Azure Industrial IoT-tjänster finns i [GitHub-databasen](https://github.com/Azure/azure-iiot-services).
Om du inte är bekant med hur Azure IoT Edge-moduler fungerar börjar du med följande artiklar:
- [Om Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge-moduler](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Ansluten fabrik

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) är en implementering av Microsofts Azure Industrial IoT-referensarkitektur som kan anpassas för att uppfylla specifika affärskrav. Den fullständiga lösningskoden är öppen källkod och tillgänglig på Connected Factory-lösningsacceleratorn GitHub-databasen. Du kan använda den som utgångspunkt för en kommersiell produkt och distribuera en förbyggd lösning till din Azure-prenumeration på några minuter. 

## <a name="factory-floor-connectivity"></a>Anslutning på fabriksgolv

OPC Twin är en IIoT-komponent som automatiserar identifiering och registrering av enheter och erbjuder fjärrstyrning av industriella enheter via REST-API:er. OPC Twin använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriksnätverket. OPC Twin gör det möjligt för IIoT-utvecklare att fokusera på att bygga IIoT-program utan att oroa dig för hur du på ett säkert sätt kommer åt de lokala datorerna.

## <a name="security"></a>Säkerhet

OPC Vault är en implementering av OPC UA Global Discovery Server (GDS) som kan konfigurera, registrera och hantera certifikatlivscykeln för OPC UA-server- och klientprogram i molnet. OPC Vault förenklar implementeringen och underhållet av säker tillgångsanslutning i industriområdet. Genom att automatisera certifikathantering frigör OPC Vault fabriksoperatörer från de manuella och komplexa processer som är associerade med anslutning och certifikathantering.

## <a name="next-steps"></a>Nästa steg

Nu när du har haft en introduktion till industriell IoT och dess komponenter, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Vad är OPC-tvilling](overview-opc-twin.md)
