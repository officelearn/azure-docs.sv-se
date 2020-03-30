---
title: Introduktion till IoT Plug and Play Preview | Microsoft-dokument
description: Läs mer om förhandsversionen av IoT Plug and Play. IoT Plug and Play baseras på ett öppet modelleringsspråk som gör det möjligt för IoT-enheter att deklarera sina funktioner. IoT-enheter presenterar den deklarationen, som kallas en enhetskapacitetsmodell, när de ansluter till molnlösningar som Azure IoT Central eller partnerprogram. Molnlösningen kan sedan automatiskt förstå enheten och börja interagera med den – allt utan att skriva någon kod.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 0399e1659fb7cc6a650c6b3c1d0189c8802d4904
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064318"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Vad är IoT Plug and Play Preview?

IoT Plug and Play Preview gör det möjligt för lösningsutvecklare att integrera enheter med sina lösningar utan att skriva någon inbäddad kod. Kärnan i IoT Plug and Play är ett modellschema för _enhetskapacitet_ som beskriver enhetsfunktioner. Det här schemat är ett JSON-dokument som är strukturerat som en uppsättning gränssnitt som innehåller definitioner av:

- _Egenskaper_ som representerar skrivskyddat och skrivskyddat/skrivskyddat för en enhet eller annan entitet. Ett enhetsserienummer kan till exempel vara en skrivskyddad egenskap och en måltemperatur på en termostat kan vara en läs-/skrivegenskap.
- _Telemetri_ som är de data som avges av en enhet, oavsett om data är en vanlig ström av sensoravläsningar, ett tillfälligt fel eller informationsmeddelande.
- _Kommandon_ som beskriver en funktion eller åtgärd som kan utföras på en enhet. Ett kommando kan till exempel starta om en gateway eller ta en bild med hjälp av en fjärrkamera.

Du kan återanvända gränssnitt över enhetskapacitetsmodeller för att underlätta samarbetet och påskynda utvecklingen.

Om du vill att IoT Plug and Play ska fungera sömlöst med [Azure Digital Twins](../digital-twins/about-digital-twins.md)definieras IoT Plug and Play-schemat med [DTDL (Digital Twin Definition Language).](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) IoT Plug and Play och DTDL är öppna för communityn och Microsoft välkomnar samarbete med kunder, partners och branschen. Båda är baserade på öppna W3C-standarder som JSON-LD och RDF, vilket möjliggör enklare införande mellan tjänster och verktyg. Dessutom kostar det ingen extra kostnad för att använda IoT Plug and Play och DTDL. Standardpriser för [Azure IoT Hub,](../iot-hub/about-iot-hub.md) [Azure IoT Central](../iot-central/core/overview-iot-central.md)och andra Azure-tjänster förblir desamma.

Lösningar som bygger på IoT Hub eller IoT Central kan dra nytta av IoT Plug and Play.

I den här artikeln beskrivs:

- De typiska rollerna som är associerade med ett projekt som använder IoT Plug and Play.
- Så här använder du IoT Plug and Play-enheter i ditt program.
- Så här utvecklar du ett IoT-enhetsprogram som stöder IoT Plug and Play.
- Så här certifierar du en IoT Plug and Play-enhet och publicerar i [katalogen Certifierad för IoT-enhet](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Användarroller

IoT Plug and Play är användbart för två typer av utvecklare:

- En _lösningsutvecklare_ ansvarar för att utveckla en IoT-lösning med Azure IoT och andra Azure-resurser och för att identifiera IoT-enheter att integrera.
- En _enhetsutvecklare_ skapar koden som körs på en enhet som är ansluten till din lösning.

## <a name="use-iot-plug-and-play-devices"></a>Använda IoT Plug and Play-enheter

Som lösningsutvecklare kan du utveckla en molnbaserad IoT-lösning som använder IoT Plug and Play-enheter. Du kan använda någon av följande Azure-tjänster:

- [IoT Central](../iot-central/core/overview-iot-central.md) - en fullständigt hanterad IoT-lösning för programvara som en tjänst som gör det enkelt att skapa produkter som förbinder de fysiska och digitala världarna.
- [IoT Hub](../iot-hub/about-iot-hub.md) - en hanterad molntjänst, som fungerar som en meddelandehubb för säker dubbelriktad kommunikation mellan ditt IoT-program och dina enheter.

Du hittar IoT Plug and Play-enheter via Azure Certified for IoT-enhetskatalogen. Varje IoT Plug and Play-enhet i katalogen har validerats och har en enhetskapacitetsmodell. Visa enhetskapacitetsmodellen för att förstå enhetens funktioner eller använda den för att simulera enheten i Azure IoT Central.

När du ansluter en IoT Plug and Play-enhet kan du visa dess enhetskapacitetsmodell, gränssnitten som ingår i modellen och telemetri, egenskaper och kommandon som definierats i dessa gränssnitt.

## <a name="develop-an-iot-device-application"></a>Utveckla ett IoT-enhetsprogram

Som enhetsutvecklare kan du utveckla en IoT-maskinvaruprodukt som stöder IoT Plug and Play. Processen omfattar två viktiga steg:

1. Definiera enhetens kapacitetsmodell och gränssnitt. Du skapar en uppsättning JSON-filer som deklarerar enhetens funktioner med hjälp av [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). En enhetskapacitetsmodell beskriver en fullständig entitet, till exempel en fysisk produkt, och definierar den uppsättning gränssnitt som implementeras av den entiteten. Gränssnitt är delade kontrakt som unikt identifierar telemetri, egenskaper och kommandon som stöds av en enhet. Gränssnitt kan återanvändas över olika enhetskapacitetsmodeller.

1. Skapa enhetsprogramvaran eller den fasta programvaran som implementerar de funktioner som deklareras i enhetskapacitetsmodellen och gränssnitten. Azure IoT SDK innehåller API:er för att implementera enhetskapacitetsmodeller.

[Tilläggspaketet Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) innehåller många funktioner som hjälper dig. Som enhetsutvecklare kan du till exempel använda ett tillägg för att generera ett skelett C-projekt från en kapacitetsmodell. Men du kan använda alla IDE för att skapa och implementera enhetskapacitetsmodeller.

## <a name="certify-an-iot-plug-and-play-device"></a>Certifiera en IoT Plug and Play-enhet

Som enhetsutvecklare kan du skicka in IoT-maskinvaruprodukter för certifiering. Du kan publicera en certifierad enhet i katalogen Certifierad för IoT-enhet. Stegen för certifieringsprocessen omfattar:

- Gå med i [Microsoft Partner Network](https://partner.microsoft.com).
- Ombord till Den certifierade för Azure IoT-portalen.
- Skicka in en IoT Plug and Play-enhetskapacitetsmodell och marknadsföringsinformation för att skapa en ny enhetspost.
- Klara automatisk uppsättning valideringstester för enheten.
- Publicera i katalogen Certifierad för IoT-enhet.

## <a name="regional-availability"></a>Regional tillgänglighet

Under den offentliga förhandsversionen är IoT Plug and Play tillgängligt i alla regioner.

## <a name="message-quotas-in-iot-hub"></a>Meddelandekvoter i IoT Hub
Under den offentliga förhandsversionen skickar IoT Plug and Play-enheter separata meddelanden per gränssnitt, vilket kan öka antalet meddelanden som räknas in i [meddelandekvoten](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Plug and Play är det föreslagna nästa steget att prova en av snabbstarterna:

- [Använda en enhetskapacitetsmodell för att skapa en IoT Plug and Play-enhet](./quickstart-create-pnp-device-windows.md)
- [Ansluta en enhet till IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Ansluta till en enhet i din lösning](./quickstart-connect-pnp-device-solution-node.md)
