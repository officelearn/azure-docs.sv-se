---
title: Ansluten fabrik-lösning funktioner – Azure | Microsoft Docs
description: En översikt över funktioner i lösningen ansluten fabrik förkonfigurerade.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: af2a2c84f9eb420a7ca9a8bd5909cbf856d29a5e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309203"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Vad är ansluten fabrik IoT lösningsaccelerator?

Ansluten fabrik är en implementering av Microsofts Azure industriella IoT-Referensarkitektur, paketeras som på öppen källkod. Du kan använda den som en startpunkt för en kommersiell produkt. Du kan distribuera en version som är inbyggda i lösningen ansluten fabrik i Azure-prenumerationen från [Azure IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com/#solutions/types/CF).

![Instrumentpanelen för lösningen för ansluten fabrik](./media/iot-accelerators-connected-factory-features/dashboard.png)

Ansluten fabrik lösningsaccelerator [koden finns på GitHub](https://github.com/Azure/azure-iot-connected-factory).

Ansluten fabrik innehåller följande funktioner:

## <a name="industrial-device-interoperability"></a>Industriella enheter samverkan

- Anslut till industriella tillgångar med en OPC UA-gränssnitt.
- Använda simulerade produktionsrader (köra OPC UA-servrar i Docker-behållare) för att se live telemetri från dem.
- Bläddra OPC UA-informationsmodellen av OPC UA-servrar från en instrumentpanel i molnet.

## <a name="remote-management"></a>Fjärrhantering

- Konfigurera dina OPC UA-tillgångar från molnet instrumentpanelen (anropa metoder, läsa och skriva data).
- Publicera och avpublicera telemetridata från dina OPC UA-resurser från en instrumentpanel i molnet.

## <a name="cloud-dashboard"></a>Cloud-instrumentpanelen

- Visa telemetri förhandsversioner direkt i en instrumentpanel i molnet.
- Visa trender i dessa data och skapa samband med hjälp av Time Series Insights Explorer-instrumentpanel.
- Se beräknade övergripande utrustning effektivitet (OEE) och (Key Performance Indicator) från en instrumentpanel i molnet.
- Visa industriella tillgångar hierarkier i en topologi i trädet och på en interaktiv karta.
- Visa, är medveten om och stänga aviseringar från en instrumentpanel i molnet.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) har utformats för att lagra, visualisera och fråga stora mängder time series-data. Ansluten fabrik använder den här tjänsten.
- Ansluten fabrik kan integreras med den här tjänsten gör att du utför djup i realtid analys av dina enhetsdata.

## <a name="rules-and-alerts"></a>Regler och aviseringar

[Konfigurera tröskelbaserade regler för aviseringar](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Slutpunkt till slutpunkt säkerhet

- Konfigurera behörigheter för användare med hjälp av rollbaserad åtkomstkontroll (RBAC).
- Slutpunkt till slutpunkt kryptering implementeras med hjälp av OPC UA-autentisering (med X.509-certifikat) samt säkerhetstoken.

## <a name="customizability"></a>Anpassningsbarhet

- Anpassa lösningen för att uppfylla specifika affärsbehov.
- Hela lösningen-källkoden finns på GitHub. Se den [förinställda lösningen ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory) lagringsplats.

## <a name="next-steps"></a>Nästa steg

Läs mer om lösningen ansluten fabrik förkonfigurerade genom att läsa följande artiklar:

* [Den förkonfigurerade genomgång av lösningen ansluten fabrik](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Distribuera en gateway för ansluten fabrik]( iot-accelerators-connected-factory-gateway-deployment.md)
