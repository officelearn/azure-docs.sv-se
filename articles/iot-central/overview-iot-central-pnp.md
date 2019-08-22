---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en SaaS lösning från slut punkt till slut punkt som du kan använda för att bygga och hantera din anpassade IoT-lösning. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0675be988e7e9560560dd07338563c9fa22fcb4c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878294"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Vad är Azure IoT Central (för hands versions funktioner)?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> [IoT plug and Play](https://aka.ms/iot-pnp-docs) -funktionerna i Azure IoT Central finns för närvarande i en offentlig för hands version. Använd inte ett IoT-Plug and Play aktiverat IoT Central program för produktions arbets belastningar. För produktions miljöer används ett IoT Central-program som skapats från en aktuell, allmänt tillgänglig program mal len.

Azure IoT Central är en fullständigt hanterad IoT-lösning för program vara som en tjänst som gör det enkelt att skapa produkter som ansluter till fysiska och digitala världar. Du kan förverkliga din vision för anslutna produkter genom att:

- härleda nya insikter från anslutna enheter och därmed kunna erbjuda bättre produkter och upplevelser för dina kunder
- skapa nya affärsmöjligheter för din organisation.

Azure IoT Central, jämfört med ett typiskt IoT-projekt:

- Minskar hanteringen av belastningen.
- Minskar drifts kostnader och omkostnader.
- Gör det enkelt att anpassa ditt program medan du arbetar med:
  - branschledande tekniker som exempelvis [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) och [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - säkerhetsfunktioner i företagsklass, till exempel kryptering från slutpunkt till slutpunkt.

Följande videoklipp visar en översikt över Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Den här artikeln beskriver för Azure IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.

## <a name="known-issues"></a>Kända problem

> [!Note]
> Dessa kända problem gäller endast för IoT Central för hands versions programmet.

- Regler har inte stöd för alla åtgärder (endast e-post).
- För komplexa typer – regler, analyser och enhets grupper stöds inte.
- Kontinuerlig data export har inte stöd för Avro-formatet (inkompatibilitet).
- Simulerade enheter stöder inte alla komplexa typer.
- Interjson stöds inte för närvarande.
- Kart panelen stöds inte för närvarande.
- Jobb stöder inte komplexa typer.
- Mat ris schema typer stöds inte.
- Export och program kopiering av program mal len stöds inte.
- Endast C-enhetens SDK och Node. js-enhet och tjänst-SDK: er stöds.
- Den är endast tillgänglig i SELECT-regioner.

## <a name="personas"></a>Profiler

Azure IoT Central-dokumentationen avser fyra personer som interagerar med ett Azure IoT Central-program:

- en _byggare_ ansvarar för att definiera vilka typer av enheter som ansluter till programmet och anpassar programmet för operatören
- en _operatör_ hanterar de enheter som är anslutna till programmet
- en _administratör_ ansvarar för administrativa uppgifter som att hantera användare och roller i programmet
- en _enhetsutvecklare_ skapar den kod som körs på en enhet som är ansluten till ditt program.

## <a name="create-your-azure-iot-central-application"></a>Skapa ditt Azure IoT Central-program

Som byggare använder du Azure IoT Central till att skapa en anpassad, molnbaserad IoT-lösning för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt Azure IoT Central-program och sedan anpassa det efter dina specifika krav i webbläsaren. Som ett verktyg använder du webbaserade verktyg för att skapa en enhets _mall_ för de enheter som ansluter till ditt program. En enhets mall är den skiss som definierar egenskaperna och beteendet för en typ av enhet, till exempel:

- Telemetri som skickas.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Egenskaper som anges av en operatör som fastställer enhetens beteende.

Den här enhets mal len innehåller:

- En _modell för enhets kapacitet_ som beskriver de funktioner som en enhet ska implementera, t. ex. telemetri som skickas och de egenskaper som den rapporterar.
- Moln egenskaper som inte lagras på enheten.
- Anpassningar, instrument paneler och formulär som är en del av ditt IoT Central-program.

### <a name="create-device-templates"></a>Skapa mallar för enheter

[IoT plug and Play](https://aka.ms/iot-pnp-docs) gör det möjligt för IoT Central att integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play är ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central för hands versions program använder enhets mallarna de här IoT-Plug and Play enhets kapacitets modeller.

Som ett verktyg har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och implementera sedan dess enhets kapacitets modell i enhets koden.
- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat) och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen och Anslut enheten till IoT Central-programmet. IoT Central hittar enhetens kapacitets modell från en lagrings plats och skapar en enkel enhets mall åt dig.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till ditt IoT Central program och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.

Som ett verktyg kan du använda IoT Central för att generera kod för test enheter för att validera dina enhets mallar.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Som byggare kan du också anpassa Azure IoT Central-användargränssnittet för de operatörer som ansvarar för den dagliga användningen av programmet. En byggare kan bl.a. göra följande anpassningar:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="connect-your-devices"></a>Anslut dina enheter

När byggaren har definierat vilka typer av enheter som kan ansluta till programmet, skapar en enhetsutvecklare koden som ska köras på enheterna. Som enhetsutvecklare kan du använda den öppna källkoden [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) från Microsoft när du skapar din enhetskod. Dessa SDK:er har brett språk-, plattforms- och protokollstöd som hjälper dig att ansluta enheter till ditt Azure IoT Central-program. Med SDK: er kan du implementera följande enhets funktioner:

- skapa en säker anslutning
- skicka telemetri
- rapportera status
- ta emot konfigurationsuppdateringar.

Mer information finns i bloggposten om [fördelar med att använda Azure IoT-SDK:er och fallgropar att undvika om du inte gör det](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Hantera ditt program

Azure IoT Central-program är helt värdbaserade hos Microsoft, vilket minskar administrationsomkostnaderna när du hanterar dina program.

Som operatör kan du använda Azure IoT Central-programmet till att hantera enheter i din Azure IoT Central-lösning. Operatörer utför uppgifter som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Som ett verktyg kan du definiera anpassade regler och åtgärder som körs via data strömning från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Administratörer hanterar åtkomst till ditt program med [användar roller och behörigheter](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central föreslår vi nästa steg:

- Förstå skillnaderna mellan [Azure IoT Central och Azure IoT-lösningsacceleratorer](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Följ en serie självstudier som visar hur du kan:
  - [som byggare – skapa en enhetsmall](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [som byggare – lägga till regler för att automatisera din lösning](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [som operatör – övervaka dina enheter](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Lägg till en enhet i lösningen som en operatör](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- Lär dig mer om [IoT plug and Play](https://aka.ms/iot-pnp-docs)
