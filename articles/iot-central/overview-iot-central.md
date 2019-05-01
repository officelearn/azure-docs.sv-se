---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en SaaS-lösningen för slutpunkt till slutpunkt som du kan använda för att skapa och hantera din anpassade IoT-lösning. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 84fa7aa006a6bc5365527dbf8043797617543590
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704543"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

Azure IoT Central är en fullständigt hanterad programvara-som-tjänst-lösning för IoT som gör det enkelt att skapa produkter som ansluter de fysiska och digital världarna. Du kan förverkliga din vision för anslutna produkter genom att:

- härleda nya insikter från anslutna enheter och därmed kunna erbjuda bättre produkter och upplevelser för dina kunder
- skapa nya affärsmöjligheter för din organisation.

Azure IoT Central, jämfört med ett typiskt IoT-projekt:

- Minskar administrationen.
- Minskar driftskostnader och omkostnader.
- Gör det enkelt att anpassa ditt program när du arbetar med:
  - branschledande tekniker som exempelvis [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) och [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - säkerhetsfunktioner i företagsklass, till exempel kryptering från slutpunkt till slutpunkt.

Följande videoklipp visar en översikt över Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Den här artikeln beskrivs för Azure IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.

## <a name="personas"></a>Profiler

Azure IoT Central dokumentationen refererar till fyra personer som interagerar med ett Azure IoT Central program:

- en _byggare_ ansvarar för att definiera vilka typer av enheter som ansluter till programmet och anpassar programmet för operatören
- en _operatör_ hanterar de enheter som är anslutna till programmet
- en _administratör_ ansvarar för administrativa uppgifter som att hantera användare och roller i programmet
- en _enhetsutvecklare_ skapar den kod som körs på en enhet som är ansluten till ditt program.

## <a name="create-your-azure-iot-central-application"></a>Skapa ditt Azure IoT Central-program

Som byggare använder du Azure IoT Central till att skapa en anpassad, molnbaserad IoT-lösning för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt program för Azure IoT Central och anpassa den efter dina specifika krav i din webbläsare. Som ett verktyg du kan använda de webbaserade verktyg för att skapa en _enheten mallen_ för enheter som ansluter till ditt program. En mall för enheten är det som definierar egenskaperna och beteendet för en typ av enhet som den:

- Telemetri som skickas.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Tröskelvärden för programmet svarar på.
- inställningar som styr beteendet för enheten.

Du kan testa dina enhetsmallar och program omedelbart med simulerade data som Azure IoT Central genererar åt dig.

Som byggare kan du också anpassa Azure IoT Central-användargränssnittet för de operatörer som ansvarar för den dagliga användningen av programmet. En byggare kan bl.a. göra följande anpassningar:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="connect-your-devices"></a>Anslut dina enheter

När byggaren har definierat vilka typer av enheter som kan ansluta till programmet, skapar en enhetsutvecklare koden som ska köras på enheterna. Som enhetsutvecklare kan du använda den öppna källkoden [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) från Microsoft när du skapar din enhetskod. Dessa SDK:er har brett språk-, plattforms- och protokollstöd som hjälper dig att ansluta enheter till ditt Azure IoT Central-program. SDK: erna hjälper dig att implementera följande funktioner för enheter:

- skapa en säker anslutning
- skicka telemetri
- rapportera status
- ta emot konfigurationsuppdateringar.

Mer information finns i bloggposten om [fördelar med att använda Azure IoT-SDK:er och fallgropar att undvika om du inte gör det](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Hantera ditt program

Azure IoT Central-program är helt värdbaserade hos Microsoft, vilket minskar administrationsomkostnaderna när du hanterar dina program.

Som operatör kan du använda Azure IoT Central-programmet till att hantera enheter i din Azure IoT Central-lösning. Operatörer göra som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Som en builder, kan du definiera anpassade regler och åtgärder som fungerar över data som strömmas från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Administratörer hantera åtkomst till ditt program med [användarroller och behörigheter](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central föreslår vi nästa steg:

- Förstå skillnaderna mellan [Azure IoT Central och Azure IoT-lösningsacceleratorer](overview-iot-options.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- Följ en serie självstudier som visar hur du kan:
  - [som byggare – skapa en enhetsmall](tutorial-define-device-type.md)
  - [som byggare – lägga till regler för att automatisera din lösning](tutorial-configure-rules.md)
  - [som byggare – anpassa programmet för dina operatörer](tutorial-customize-operator.md)
  - [som operatör – övervaka dina enheter](tutorial-monitor-devices.md)
  - [som operatör – lägga till en verklig enhet i lösningen](tutorial-add-device.md)
  - [som enhetsutvecklare – skapa kod för dina enheter](tutorial-add-device.md#prepare-the-client-code).
