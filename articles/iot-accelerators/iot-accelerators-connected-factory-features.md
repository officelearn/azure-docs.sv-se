---
title: Anslutna fabrikslösningsfunktioner – Azure | Microsoft-dokument
description: I den här artikeln beskrivs en översikt över funktionerna i den förkonfigurerade lösningen för ansluten fabrik, till exempel molninstrumentpanel, regler och aviseringar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820100"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Vad är Connected Factory IoT-lösningsaccelerator?

Connected Factory är en implementering av Microsofts Azure Industrial IoT-referensarkitektur, paketerad som på lösning med öppen källkod. Du kan använda den som utgångspunkt för en kommersiell produkt. Du kan distribuera en förbyggd version av Connected Factory-lösningen till din Azure-prenumeration från [Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/#solutions/types/CF).

![Instrumentpanel för ansluten fabrikslösning](./media/iot-accelerators-connected-factory-features/dashboard.png)

Acceleratorkoden för Connected [Factory-lösningen finns på GitHub](https://github.com/Azure/azure-iot-connected-factory).

Connected Factory innehåller följande funktioner:

## <a name="industrial-device-interoperability"></a>Driftskompatibilitet för industrienheter

- Anslut till industritillgångar med ett OPC UA-gränssnitt.
- Använd de simulerade produktionslinjerna (kör OPC UA-servrar i Docker-behållare) för att se live-telemetri från dem.
- Bläddra i OPC UA-informationsmodellen för OPC UA-servrar från en molninstrumentpanel.

## <a name="remote-management"></a>Fjärrhantering

- Konfigurera dina OPC UA-resurser från molninstrumentpanelen (samtalsmetoder, läsa och skriva data).
- Publicera och avpublicera telemetridata från dina OPC UA-tillgångar från en molninstrumentpanel.

## <a name="cloud-dashboard"></a>Instrumentpanel i molnet

- Visa förhandsgranskningar av telemetri direkt i en molninstrumentpanel.
- Visa trender i telemetridata och skapa korrelationer med hjälp av instrumentpanelen i Time Series Insights Explorer.
- Se beräknade övergripande utrustningseffektivitet (OEE) och KPI:er (Key Performance Indicators) från en molninstrumentpanel.
- Visa hierarkier för industriella tillgångar i en trädtopologi och på en interaktiv karta.
- Visa, bekräfta och stänga aviseringar från en molninstrumentpanel.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) är byggd för att lagra, visualisera och fråga stora mängder tidsseriedata. Connected Factory utnyttjar den här tjänsten.
- Connected Factory integreras med den här tjänsten så att du kan utföra djupgående analyser i realtid av dina enhetsdata.

## <a name="rules-and-alerts"></a>Regler och varningar

[Konfigurera tröskelbaserade regler för aviseringar](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Heltäckande säkerhet

- Konfigurera säkerhetsbehörigheter för användare med hjälp av rollbaserad åtkomstkontroll (RBAC).
- End-to-end-kryptering implementeras med OPC UA-autentisering (med X.509-certifikat) samt säkerhetstoken.

## <a name="customizability"></a>Customizability

- Anpassa lösningen för att uppfylla specifika affärskrav.
- Fullständig lösnings källkod tillgänglig på GitHub. Se den [förkonfigurerade lösningsdatabasen för ansluten fabrik.](https://github.com/Azure/azure-iot-connected-factory)

## <a name="next-steps"></a>Nästa steg

Mer information om acceleratorn för Connected Factory-lösningen finns i Quickstart [Prova en molnbaserad lösning för att hantera mina industriella IoT-enheter](quickstart-connected-factory-deploy.md).
