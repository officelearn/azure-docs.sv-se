---
title: Översikt över Azure industriella IoT | Microsoft Docs
description: Översikt över industriella IoT
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bda40470e3ccf3a5d7b23dca38b21090e864b16a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491756"
---
# <a name="what-is-industrial-iot-iiot"></a>Vad är industriella IoT (IIoT)

IIoT är industriella Internet of Things. IIoT förbättrar industriella effektiviteten genom tillämpning av IoT inom tillverkningsindustrin. 

## <a name="improve-industrial-efficiencies"></a>Förbättra effektiviteten industriella

Förbättra verksamhetens produktivitet och lönsamhet med en lösningsaccelerator för ansluten fabrik. Anslut och övervaka industriell utrustning och enheter i molnet, även maskiner som redan används på verkstadsgolvet. Analysera IoT-data och få insikter som kan hjälpa dig att förbättra produktiviteten på hela verkstadsgolvet.

Minska tidskrävande process för att komma åt factory våning datorer med OPC-Twin och fokusera din tid på att skapa IIoT lösningar. Effektivisera certifikathantering och industriella tillgångar integrering med OPC-valv och är säker på att plats-anslutning är skyddad. Dessa mikrotjänster tillhandahåller en REST-liknande API ovanpå [Azure industriella IoT komponenter](https://github.com/Azure/azure-iiot-opc-ua). Tjänst-API ger dig kontroll över edge-modul-funktionerna. 

![Industriella IoT-översikt](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Mer information om Azure industriella IoT-tjänster finns i GitHub [databasen](https://github.com/Azure/azure-iiot-services).
Om du är bekant med hur Azure IoT Edge-moduler fungerar kan du börja med följande artiklar:
- [Om Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge-moduler](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Ansluten fabrik

[Ansluten fabrik](../iot-accelerators/iot-accelerators-connected-factory-features.md) är en implementering av Microsofts Azure industriella IoT-referensarkitekturen som kan anpassas för att uppfylla specifika affärsbehov. Hela lösningen koden är öppen källkod och tillgängligt på ansluten fabrik solution accelerator GitHub-lagringsplatsen. Du kan använda den som utgångspunkt för en kommersiell produkt och distribuera en fördefinierad lösning i din Azure-prenumeration på några minuter. 

## <a name="factory-floor-connectivity"></a>Factory våning anslutning

OPC-Twin är en komponent med IIoT som automatiserar Enhetsidentifiering och registrering och erbjuder fjärrstyrning av industriella enheter via REST API: er. OPC-Twin använder Azure IoT Edge och IoT Hub för att ansluta molnet och factory nätverket. OPC-Twin kan IIoT utvecklarna kan fokusera på att bygga IIoT program utan att behöva bekymra dig om hur säker åtkomst till lokala datorer.

## <a name="security"></a>Säkerhet

OPC-valv är en implementering av OPC UA globala identifiering Server (GDS) som kan konfigurera, registrera och hantera livscykeln för användarcertifikatet för OPC UA-servern och klientprogram i molnet. OPC Vault förenklar implementering och underhåll av säker plats-anslutning i industriella utrymme. Genom att automatisera certifikathantering Frigör OPC Vault factory operatorerna från manuell och komplexa processer som är associerade med anslutning och certifikathantering.

## <a name="next-steps"></a>Nästa steg

Nu när du har haft en introduktion till industriella IoT och dess komponenter, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Vad är OPC-Twin](overview-opc-twin.md)
