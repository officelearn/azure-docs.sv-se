---
title: Introduktion till IoT Plug and Play Preview | Microsoft Docs
description: Läs mer om IoT Plug and Play Preview. IoT Plug and Play baseras på ett öppet modell språk som gör det möjligt för IoT-enheter att deklarera sina funktioner. IoT-enheter visar att deklarationen kallas en modell för enhets kapacitet när de ansluter till moln lösningar som Azure IoT Central eller partner program. Moln lösningen kan sedan automatiskt förstå enheten och börja interagera med den – utan att behöva skriva någon kod.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 27063702729d8a61ab8919c2685fcfc6f743ce09
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858989"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Vad är IoT Plug and Play Preview?

Med IoT Plug and Play Preview kan lösningar utvecklare integrera enheter med sina lösningar utan att behöva skriva någon inbäddad kod. I IoT-Plug and Play är ett modell schema för _enhets kapacitet_ som beskriver enhets funktioner. Det här schemat är ett JSON-dokument som är strukturerat som en uppsättning gränssnitt som innehåller definitioner av:

- _Egenskaper_ som representerar Skriv-och Läs-och skriv status för en enhet eller annan entitet. Ett enhets serie nummer kan exempelvis vara en skrivskyddad egenskap och en mål temperatur på en termostat kan vara en Läs-och skrivbar egenskap.
- _Telemetri_ som är data som skickas av en enhet, oavsett om datan är en vanlig ström av sensor avläsningar, ett tillfälligt fel eller informations meddelande.
- _Kommandon_ som beskriver en funktion eller åtgärd som kan utföras på en enhet. Ett kommando kan till exempel starta om en gateway eller ta en bild med en fjärran sluten kamera.

Du kan återanvända gränssnitt mellan enhets kapacitets modeller för att förenkla samarbetet och påskynda utvecklingen.

För att kunna göra IoT Plug and Play fungerar sömlöst med [Azure Digitals](../digital-twins/about-digital-twins.md), definieras IoT plug and Play-schemat med hjälp av det [digitala DTDL (Digital Definition Language)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Plug and Play och DTDL är öppna för communityn och Microsoft välkomnar samarbetet med kunder, partner och branschen. Båda baseras på öppna W3C-standarder som JSON-LD och RDF, vilket möjliggör enklare införande mellan tjänster och verktyg. Det finns dessutom ingen extra kostnad för att använda IoT Plug and Play och DTDL. Standardpriserna för [azure IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/overview-iot-central.md)och andra Azure-tjänster förblir desamma.

Lösningar som bygger på IoT Hub eller IoT Central kan dra nytta av IoT-Plug and Play.

Den här artikeln beskriver:

- Vanliga roller som är kopplade till ett projekt som använder IoT Plug and Play.
- Använda IoT Plug and Play-enheter i ditt program.
- Utveckla ett IoT-enhets program som stöder IoT Plug and Play.
- Så här certifierar du en IoT Plug and Play-enhet och publicerar den till [certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Användarroller

IoT Plug and Play är användbart för två typer av utvecklare:

- En _lösnings utvecklare_ ansvarar för att utveckla en IoT-lösning med Azure IoT och andra Azure-resurser och för att identifiera IoT-enheter som ska integreras.
- En _enhets utvecklare_ skapar den kod som körs på en enhet som är ansluten till din lösning.

## <a name="use-iot-plug-and-play-devices"></a>Använda IoT Plug and Play-enheter

Som en lösnings utvecklare kan du utveckla en IoT-lösning i molnet som använder IoT Plug and Play-enheter. Du kan använda någon av följande Azure-tjänster:

- [IoT Central](../iot-central/overview-iot-central.md) – en fullständigt hanterad IoT-lösning för program vara som en tjänst som gör det enkelt att skapa produkter som ansluter till fysiska och digitala världar.
- [IoT Hub](../iot-hub/about-iot-hub.md) – en hanterad moln tjänst, som fungerar som en meddelande hubb för säker, dubbelriktad kommunikation mellan IoT-programmet och dina enheter.

Du kan hitta IoT Plug and Play-enheter via Azure-certifierad för IoT-katalogen. Varje IoT Plug and Play-enhet i katalogen har verifierats och har en enhets kapacitets modell. Visa enhetens kapacitets modell för att förstå enhetens funktioner eller Använd den för att simulera enheten i Azure IoT Central.

När du ansluter en IoT Plug and Play-enhet kan du Visa enhetens kapacitets modell, gränssnitten som ingår i modellen och telemetri, egenskaper och kommandon som definierats i dessa gränssnitt.

## <a name="develop-an-iot-device-application"></a>Utveckla ett IoT-enhets program

Som enhets utvecklare kan du utveckla en IoT-maskin vara som stöder IoT Plug and Play. Processen omfattar två viktiga steg:

1. Definiera enhetens kapacitets modell och gränssnitt. Du skapar en uppsättning JSON-filer som deklarerar enhetens funktioner med hjälp av [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). En enhets kapacitets modell beskriver en fullständig entitet, till exempel en fysisk produkt, och definierar den uppsättning gränssnitt som implementeras av entiteten. Gränssnitt är delade kontrakt som unikt identifierar telemetri, egenskaper och kommandon som stöds av en enhet. Gränssnitt kan återanvändas över olika enhets kapacitets modeller.

1. Redigera enhets program varan eller den inbyggda program varan som implementerar de funktioner som deklareras i enhetens kapacitets modell och gränssnitt. Azure IoT SDK innehåller API: er för att implementera enhets kapacitets modeller.

[Azure IoT Tools för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack innehåller många funktioner som hjälper dig. Som enhets utvecklare kan du till exempel använda ett tillägg för att generera ett Skeleton C-projekt från en kapacitets modell. Du kan dock använda valfri IDE för att redigera och implementera enhets kapacitets modeller.

## <a name="certify-an-iot-plug-and-play-device"></a>Certifiera en IoT Plug and Play-enhet

Som enhets utvecklare kan du skicka IoT-maskinvaruprodukter för certifiering. Du kan publicera en certifierad enhet i katalogen certifierad för IoT-enheter. Stegen i certifierings processen omfattar:

- Gå med i [Microsoft Partner Network](https://partner.microsoft.com).
- Publicera till certifierad för Azure IoT-portalen.
- Skicka en IoT Plug and Play enhets kapacitets modell och marknadsförings information för att skapa en ny enhets post.
- Skicka en automatiserad uppsättning verifierings test för enheten.
- Publicera till den certifierade katalogen för IoT-enheter.

## <a name="regional-availability"></a>Regional tillgänglighet

Under den offentliga för hands versionen är IoT Plug and Play tillgängligt i regionerna Nord Europa, centrala USA och Japan, östra. Se till att du skapar navet i någon av dessa regioner.

## <a name="message-quotas-in-iot-hub"></a>Meddelande kvoter i IoT Hub
Under den offentliga för hands versionen skickar IoT Plug and Play-enheter separata meddelanden per gränssnitt, vilket kan öka antalet meddelanden som räknas mot din [meddelande kvot](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Plug and Play är det föreslagna nästa steg att prova en av snabb starterna:

- [Använd en modell för enhets kapacitet för att skapa en IoT Plug and Play-enhet](./quickstart-create-pnp-device.md)
- [Anslut en enhet till IoT Hub](./quickstart-connect-pnp-device.md)
- [Anslut till en enhet i din lösning](./quickstart-connect-pnp-device-solution.md)
