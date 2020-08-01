---
title: IoT Plug and Play-arkitektur | Microsoft Docs
description: Som Solution Builder förstår du viktiga arkitektur element i IoT-Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f656de0bb2e5244e137ae21a6d7af88f3430b12c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475693"
---
# <a name="iot-plug-and-play-preview-architecture"></a>Arkitektur för IoT Plug and Play-förhandsversion

IoT Plug and Play Preview gör det möjligt för lösnings byggare att integrera smarta enheter med sina lösningar utan manuell konfiguration. I IoT Plug and Play är en enhets _modell_ som beskriver enhetens funktioner för ett IoT plug and Play-aktiverat program. Den här modellen är strukturerad som en uppsättning gränssnitt som definierar:

- _Egenskaper_ som representerar ett skrivskyddat eller skrivbart tillstånd för en enhet eller annan enhet. Ett enhets serie nummer kan till exempel vara en skrivskyddad egenskap och en mål temperatur på en termostat kan vara en skrivbar egenskap.
- _Telemetri_ som är data som skickas av en enhet, oavsett om datan är en vanlig ström av sensor avläsningar, ett tillfälligt fel eller ett informations meddelande.
- _Kommandon_ som beskriver en funktion eller åtgärd som kan utföras på en enhet. Ett kommando kan till exempel starta om en gateway eller ta en bild med en fjärran sluten kamera.

Varje modell och gränssnitt har ett unikt ID.

Följande diagram visar viktiga element i en IoT Plug and Play-lösning:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug and Play-arkitektur":::

## <a name="model-repository"></a>Modell databas

[Modell databasen](./concepts-model-repository.md) är en lagrings plats för modell-och gränssnitts definitioner. Du definierar modeller och gränssnitt med hjälp av [DTDL (Digital enformal Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl).

Med webb gränssnittet kan du hantera modeller och gränssnitt.

Modell databasen använder RBAC så att du kan begränsa åtkomsten till gränssnitts definitioner.

## <a name="devices"></a>Egenskaper

En Device Builder implementerar koden som ska köras på en smart IoT-enhet med hjälp av en av [Azure IoT-enhetens SDK](./libraries-sdks.md): er. Enhets-SDK: erna hjälper enhets byggare att:

- Anslut säkert till en IoT-hubb.
- Registrera enheten med IoT-hubben och meddela modell-ID: t som identifierar den samling av gränssnitt som enheten implementerar.
- Uppdatera egenskaperna som definierats i de DTDL-gränssnitt som enheten implementerar. Dessa egenskaper implementeras med hjälp av digitala dubbla, som hanterar synkroniseringen med IoT Hub.
- Lägg till kommando hanterare för de kommandon som definierats i de DTDL-gränssnitt som enheten implementerar.
- Skicka telemetri till IoT Hub.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) är en moln värd tjänst som fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan din IoT-lösning och de enheter som hanteras.

En IoT-hubb:

- Gör modell-ID: t implementerat av en enhet som är tillgänglig för en server dels lösning.
- Underhåller den digitala enhet som är kopplad till varje Plug and Play enhet som är ansluten till hubben.
- Vidarebefordrar telemetri-strömmar till andra tjänster för bearbetning eller lagring.
- Dirigerar digitala dubbla ändrings händelser till andra tjänster för att aktivera enhets övervakning.

## <a name="backend-solution"></a>Backend-lösning

En server dels lösning övervakar och kontrollerar anslutna enheter genom att interagera med digitala dubbla i IoT Hub. Använd en av tjänst-SDK: erna för att implementera din server dels lösning. För att förstå funktionerna hos en ansluten enhet är lösningens Server del:

1. Hämtar modell-ID: t som enheten är registrerad på IoT Hub.
1. Använder modell-ID: t för att hämta gränssnitts definitioner från vilken modell databas som helst.
1. Använder modell tolkare för att extrahera information från gränssnitts definitionerna.

Server dels lösningen kan använda informationen från gränssnitts definitionerna för att:

- Läsa egenskaps värden som rapporteras av enheter.
- Uppdatera skrivbara egenskaper på en enhet.
- Anropa kommandon som implementeras av en enhet.
- Förstå formatet på telemetri som skickas av en enhet.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över arkitekturen i en IoT Plug and Play-lösning är nästa steg att lära dig mer om:

- [Modell databasen](./concepts-model-repository.md)
- [Digital integrering av dubbla modeller](./concepts-model-discovery.md)
- [Utveckla för IoT Plug and Play](./concepts-developer-guide.md)
