---
title: Översikt över Azures industriella IoT | Microsoft Docs
description: Den här artikeln ger en översikt över industriella IoT. Den förklarar den anslutna fabriken, fabrikens golv anslutning och säkerhets komponenter i IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828130"
---
# <a name="what-is-industrial-iot-iiot"></a>Vad är industriella IoT (IIoT)

IIoT är den industriella Sakernas Internet. IIoT förbättrar bransch effektiviteten genom program varan för IoT i tillverknings branschen. 

## <a name="improve-industrial-efficiencies"></a>Förbättra industriella effektivitets vinster

Förbättra din operativa produktivitet och lönsamhet med en ansluten fabriks lösnings Accelerator. Anslut och övervaka industriell utrustning och enheter i molnet, även maskiner som redan används på verkstadsgolvet. Analysera IoT-data och få insikter som kan hjälpa dig att förbättra produktiviteten på hela verkstadsgolvet.

Minska tids krävande processen för att komma åt fabriks maskiner med OPC, och fokusera din tid på att bygga IIoT-lösningar. Effektivisera certifikat hanteringen och industriell till gångs integrering med OPC-valvet och var säker på att till gångs anslutningen är säker. Dessa mikrotjänster ger en REST-liknande API ovanpå [Azures industriella IoT-komponenter](https://github.com/Azure/azure-iiot-opc-ua). Med tjänst-API: t får du kontroll över funktionerna i Edge-modulen. 

![Översikt över industriella IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Mer information om Azure industriella IoT-tjänster finns i GitHub- [lagringsplatsen](https://github.com/Azure/azure-iiot-services).
Om du inte är bekant med hur Azure IoT Edge moduler fungerar börjar du med följande artiklar:
- [Om Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge moduler](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Ansluten fabrik

[Ansluten fabrik](../iot-accelerators/iot-accelerators-connected-factory-features.md) är en implementering av Microsofts referens arkitektur för Azures industriella IoT som kan anpassas efter specifika affärs behov. Den fullständiga lösnings koden är öppen källkod och tillgänglig på den anslutna fabriken Solution Accelerator GitHub-lagringsplatsen. Du kan använda den som utgångspunkt för en kommersiell produkt och distribuera en fördefinierad lösning i din Azure-prenumeration på några minuter. 

## <a name="factory-floor-connectivity"></a>Fabriks anslutning på fabriken

OPC dubbla är en IIoT-komponent som automatiserar identifiering och registrering av enheter och ger fjärr styrning av industriella enheter via REST API: er. OPC är dubbla, använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriks nätverket. OPC med dubbla låter IIoT utvecklare fokusera på att skapa IIoT-program utan att behöva oroa dig för säker åtkomst till lokala datorer.

## <a name="security"></a>Säkerhet

OPC-valvet är en implementering av OPC UA global Discovery Server (GDS) som kan konfigurera, registrera och hantera certifikat livs cykeln för OPC UA-Server och klient program i molnet. OPC-valvet fören klar implementeringen och underhållet av säker till gångs anslutning i det industriella utrymmet. Genom att automatisera certifikat hanteringen frigör OPC-valvet fabriks operatörer från de manuella och komplexa processerna som är kopplade till anslutningen och certifikat hanteringen.

## <a name="next-steps"></a>Nästa steg

Nu när du har haft en introduktion till den industriella IoT-komponenten och dess komponenter, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Vad är OPCt](overview-opc-twin.md)
