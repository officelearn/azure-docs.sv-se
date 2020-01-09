---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: a1aa2f12f62a95ac14750c821079df2bac46e8ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434960"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central är en IoT app-plattform som minskar den börda och kostnad som är kopplad till utveckling, hantering och underhåll av IoT-lösningar i företags klass. Genom att välja att skapa med Azure IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

Det lättanvända gränssnittet gör det enkelt att övervaka enhets villkor, skapa regler och hantera miljon tals enheter och deras data under deras livs cykel. Dessutom kan du agera på enhets insikter genom att utöka IoT Intelligence till branschspecifika program.

Den här artikeln beskriver för Azure IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.

## <a name="personas"></a>Profiler

Azure IoT Central-dokumentationen avser fyra personer som interagerar med ett Azure IoT Central-program:

- en _byggare_ ansvarar för att definiera vilka typer av enheter som ansluter till programmet och anpassar programmet för operatören
- en _operatör_ hanterar de enheter som är anslutna till programmet
- En _administratör_ ansvarar för uppgifter som hantering av [användar roller och behörigheter](howto-administer.md) i programmet.
- en _enhetsutvecklare_ skapar den kod som körs på en enhet som är ansluten till ditt program.

## <a name="create-your-azure-iot-central-application"></a>Skapa ditt Azure IoT Central-program

Som byggare använder du Azure IoT Central till att skapa en anpassad, molnbaserad IoT-lösning för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt Azure IoT Central-program och sedan anpassa det efter dina specifika krav i webbläsaren. Som ett verktyg använder du webbaserade verktyg för att skapa en _enhets mall_ för de enheter som ansluter till ditt program. En enhets mall är den skiss som definierar egenskaperna och beteendet för en typ av enhet, till exempel:

- Telemetri som skickas.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Tröskelvärden som programmet svarar på.
- inställningar som styr beteendet för enheten.

Du kan testa dina enhetsmallar och program omedelbart med simulerade data som Azure IoT Central genererar åt dig.

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

Administratörer hanterar åtkomst till ditt program med [användar roller och behörigheter](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central föreslår vi nästa steg:

- Förstå tillgängliga [Azure-tekniker och-tjänster för att skapa IoT-lösningar](../../iot-fundamentals/iot-services-and-technologies.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- Följ en serie självstudier som visar hur du kan:
  - [som byggare – skapa en enhetsmall](tutorial-define-device-type.md)
  - [som byggare – lägga till regler för att automatisera din lösning](tutorial-configure-rules.md)
  - [som byggare – anpassa programmet för dina operatörer](tutorial-customize-operator.md)
  - [som operatör – övervaka dina enheter](tutorial-monitor-devices.md)
  - [som operatör – lägga till en verklig enhet i lösningen](tutorial-add-device.md)
  - [som enhetsutvecklare – skapa kod för dina enheter](tutorial-add-device.md#prepare-the-client-code).
