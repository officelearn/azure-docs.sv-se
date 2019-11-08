---
title: Funktioner för ansluten fabriks lösningen – Azure | Microsoft Docs
description: I den här artikeln beskrivs en översikt över funktionerna i den förkonfigurerade lösningen ansluten fabrik, till exempel moln instrument panel, regler och aviseringar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820100"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Vad är anslutet IoT Solution Accelerator för fabriks anslutning?

Connected Factory är en implementering av Microsoft Azures referens arkitektur för industriella IoT som är paketerad som en lösning med öppen källkod. Du kan använda den som utgångs punkt för en kommersiell produkt. Du kan distribuera en fördefinierad version av den anslutna fabriks lösningen till din Azure-prenumeration från [Azure IoT Solution-acceleratorer](https://www.azureiotsolutions.com/#solutions/types/CF).

![Instrument panel för ansluten fabriks lösning](./media/iot-accelerators-connected-factory-features/dashboard.png)

Den anslutna fabriks lösnings acceleratorns [kod finns på GitHub](https://github.com/Azure/azure-iot-connected-factory).

Ansluten fabrik innehåller följande funktioner:

## <a name="industrial-device-interoperability"></a>Interoperabilitet för industriella enheter

- Anslut till industriella tillgångar med OPC UA-gränssnitt.
- Använd de simulerade produktions linjerna (som kör OPC UA-servrar i Docker-behållare) om du vill visa Live-telemetri från dem.
- Bläddra i OPC UA-informations modell för OPC UA-servrar från en moln instrument panel.

## <a name="remote-management"></a>Fjärrhantering

- Konfigurera OPC UA-tillgångar från molninstrumentpanelen (anropa metoder, läsa och skriva data).
- Publicera och avpublicera telemetridata från dina OPC UA-tillgångar från en molninstrumentpanel.

## <a name="cloud-dashboard"></a>Molninstrumentpanel

- Visa för hands versionerna av telemetri direkt i en instrument panel i molnet.
- Visa trender i telemetridata och skapa korrelationer med hjälp av Time Series Insights Explorer-instrumentpanelen.
- Se Beräknad total utrustnings effektivitet (OEE) och nyckeltal (KPI: er) från en instrument panel i molnet.
- Visa hierarkier för industriella till gångar i en träd topologi och på en interaktiv karta.
- Visa, bekräfta och Stäng aviseringar från en instrument panel i molnet.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) har skapats för att lagra, visualisera och skicka frågor till stora mängder tids serie data. Ansluten fabrik utnyttjar den här tjänsten.
- Ansluten fabrik integreras med den här tjänsten och gör att du kan utföra djup analys av enhets data i real tid.

## <a name="rules-and-alerts"></a>Regler och aviseringar

[Konfigurera tröskelbaserade regler för aviseringar](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Säkerhet slutpunkt till slutpunkt

- Konfigurera säkerhetsbehörigheter för användare med hjälp av rollbaserad åtkomstkontroll (RBAC).
- Kryptering från slut punkt till slut punkt implementeras med hjälp av OPC UA-autentisering (med X. 509-certifikat) samt säkerhetstoken.

## <a name="customizability"></a>Anpassningsbarhet

- Anpassa lösningen efter specifika affärs behov.
- Fullständig lösnings källa – kod finns på GitHub. Se lagrings platsen för den [förkonfigurerade lösningen ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Nästa steg

Mer information om den anslutna fabriks lösnings acceleratorn finns i snabb start [prova en molnbaserad lösning för att hantera mina industriella IoT-enheter](quickstart-connected-factory-deploy.md).
