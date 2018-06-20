---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en komplett SaaS-lösning som du kan använda när du skapar och hanterar din anpassade IoT-lösning. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8c369ab05059e57f2e2a98339052c27292ac7c0d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628784"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

Microsoft Azure IoT Central är en fullständigt hanterad IoT-lösning för programvara som en tjänst. Den gör det enklare att skapa produkter som sammanför det fysiska och digitala arbetslivet. Du kan förverkliga din vision för anslutna produkter genom att:

- härleda nya insikter från anslutna enheter och därmed kunna erbjuda bättre produkter och upplevelser för dina kunder
- skapa nya affärsmöjligheter för din organisation.

I jämförelse med ett typiskt IoT-projekt blir det enklare att hantera en IoT-lösning med Azure IoT Central eftersom du kan:

- minska administrationen
- sänka driftskostnader och omkostnader
- göra det enkelt att anpassa program samtidigt som du använder dig av:
  - branschledande tekniker som exempelvis [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) och [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - säkerhetsfunktioner i företagsklass, till exempel kryptering från slutpunkt till slutpunkt.

Följande videoklipp visar en översikt över Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

I resten av den här artikeln beskrivs följande i Azure IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.

## <a name="personas"></a>Profiler

Azure IoT Central-dokumentationen refererar till fyra profiler som vanligtvis interagerar med ett Azure IoT Central-program:

- en _byggare_ ansvarar för att definiera vilka typer av enheter som ansluter till programmet och anpassar programmet för operatören
- en _operatör_ hanterar de enheter som är anslutna till programmet
- en _administratör_ ansvarar för administrativa uppgifter som att hantera användare och roller i programmet
- en _enhetsutvecklare_ skapar den kod som körs på en enhet som är ansluten till ditt program.

## <a name="create-your-azure-iot-central-application"></a>Skapa ditt Azure IoT Central-program

Som byggare använder du Azure IoT Central till att skapa en anpassad, molnbaserad IoT-lösning för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt Azure IoT Central-program och sedan anpassa det efter dina specifika krav direkt i webbläsaren. Som Azure IoT Central-byggare kan du använda webbaserade verktyg till att skapa en _enhetsmall_ för enheter som ansluter till ditt program. En enhetsmall är kopian av en enhetsmodell som alla enheter som har skapats från enhetsmallen delar. En enhetsmall definierar egenskaper och beteende för en enhetstyp, till exempel:

- telemetrin den skickar
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet
- de tröskelvärden som gäller för programmet
- inställningar som styr beteendet för enheten.

Du kan testa dina enhetsmallar och program omedelbart med simulerade data som Azure IoT Central genererar åt dig.

Som byggare kan du också anpassa Azure IoT Central-användargränssnittet för de operatörer som ansvarar för den dagliga användningen av programmet. En byggare kan bl.a. göra följande anpassningar:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="connect-your-devices"></a>Anslut dina enheter

När byggaren har definierat vilka typer av enheter som kan ansluta till programmet, skapar en enhetsutvecklare koden som ska köras på enheterna. Som enhetsutvecklare kan du använda den öppna källkoden [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) från Microsoft när du skapar din enhetskod. Dessa SDK:er har brett språk-, plattforms- och protokollstöd som hjälper dig att ansluta enheter till ditt Azure IoT Central-program. Med SDK:erna kan du utföra följande uppgifter på den enhet som är ansluten till Azure IoT Central:

- skapa en säker anslutning
- skicka telemetri
- rapportera status
- ta emot konfigurationsuppdateringar.

Mer information finns i bloggposten om [fördelar med att använda Azure IoT-SDK:er och fallgropar att undvika om du inte gör det](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Hantera ditt program

Azure IoT Central-program är helt värdbaserade hos Microsoft, vilket minskar administrationsomkostnaderna när du hanterar dina program.

Som operatör kan du använda Azure IoT Central-programmet till att hantera enheter i din Azure IoT Central-lösning. Operatörer kan bl.a. utföra följande uppgifter:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

En byggare kan definiera anpassade regler och åtgärder som fungerar i strömmande data på enhetsmallnivå. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Administratörer kan hantera åtkomst till ditt program med [användarroller och behörigheter](howto-administer.md).

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
