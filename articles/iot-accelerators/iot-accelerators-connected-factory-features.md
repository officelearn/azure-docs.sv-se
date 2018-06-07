---
title: Ansluten Factory lösning funktioner - Azure | Microsoft Docs
description: En översikt över funktionerna i anslutet fabriken förkonfigurerade lösningen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: 3478217771418ab31772d6a42a7ed8d8a2e8069a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626504"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Vad är ansluten Factory IoT solution accelerator?

Anslutna fabriken är en implementering av Microsofts Azure industriella IoT Referensarkitektur, paketeras som på öppen källkod lösning. Du kan använda den som en startpunkt för en kommersiell produkt. Du kan distribuera en förskapad version av anslutna Factory-lösningen till din Azure-prenumeration från [Azure IoT solution Accelerator](https://www.azureiotsolutions.com/#solutions/types/CF).

![Anslutna Factory lösning instrumentpanelen](./media/iot-accelerators-connected-factory-features/dashboard.png)

Anslutna fabriken innehåller följande funktioner:

## <a name="industrial-device-interoperability"></a>Industriella enheten samverkan

- Ansluta till industriella tillgångar med ett OPC UA-gränssnitt.
- Använd simulerade produktion raderna (som kör OPC UA servrar i behållare med Docker) om du vill se live telemetri från dem.
- Bläddra OPC UA informationsmodellen OPC UA-servrar från en instrumentpanel i molnet.

## <a name="remote-management"></a>Fjärrhantering

- Konfigurera dina OPC UA tillgångar från molnet instrumentpanelen (anrop metoder, läsa och skriva data).
- Publicera och avpublicera telemetridata från dina OPC UA tillgångar från en instrumentpanel i molnet.

## <a name="cloud-dashboard"></a>Cloud-instrumentpanelen

- Visa telemetri förhandsgranskningar direkt i en instrumentpanel i molnet.
- Visa trender i telemetridata och skapa samband med hjälp av instrumentpanelen tid serien insikter Explorer.
- Se beräknade övergripande utrustning effektivitet (OEE) och (Key Performance Indicator) från en instrumentpanel i molnet.
- Visa industriella tillgången hierarkier i en topologi i trädet och på en interaktiv karta.
- Visa, bekräfta och stänga aviseringar från en instrumentpanel i molnet.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure tid serien insikter](../time-series-insights/time-series-insights-overview.md) är utformat för att lagra, visualisera och fråga stora mängder time series-data. Ansluten Factory använder den här tjänsten.
- Anslutna fabriken integrerar med den här tjänsten som gör att du utför djup realtid analys av dina data på enheten.

## <a name="rules-and-alerts"></a>Regler och aviseringar

[Konfigurera tröskelvärde-baserade regler för aviseringar](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Slutpunkt till slutpunkt säkerhet

- Konfigurera behörigheter för användare med hjälp av rollbaserad åtkomstkontroll (RBAC).
- Kryptering från slutpunkt till slutpunkt implementeras med hjälp av OPC UA autentisering (med X.509-certifikat) samt säkerhetstoken.

## <a name="customizability"></a>Anpassningsbarheten

- Anpassa lösningen för att uppfylla specifika krav i företaget.
- Fullständig lösning-källkod finns på GitHub. Finns det [anslutna Factory förkonfigurerade lösningen](https://github.com/Azure/azure-iot-connected-factory) databasen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den anslutna Factory förkonfigurerade lösningen genom att läsa följande artiklar:

* [Anslutna Factory förkonfigurerade lösningen genomgång](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Distribuera en gateway för ansluten fabrik]( iot-accelerators-connected-factory-gateway-deployment.md)
