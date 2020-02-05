---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som fören klar skapandet av IoT-lösningar och hjälper till att minska belastningen och kostnaden för IoT-hanterings åtgärder och utveckling. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 27862ce11785f1b264d9f0238a9bae2684c87d02
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989934"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Vad är Azure IoT Central (för hands versions funktioner)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> [IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) -funktionerna i Azure IoT Central finns för närvarande i en offentlig för hands version. Använd inte en IoT-Plug and Play som är aktive rad IoT Central [program mal len](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) för produktions arbets belastningar. För produktions miljöer används ett IoT Central-program som skapats från en aktuell, allmänt tillgänglig [program mal len](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

IoT Central är en IoT-programplattform som minskar belastningen och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företags klass. Genom att välja att skapa med IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

Med webb gränssnittet kan du övervaka enhets villkor, skapa regler och hantera miljon tals enheter och deras data under deras livs cykel. Dessutom kan du agera på enhets insikter genom att utöka IoT Intelligence till branschspecifika program.

Den här artikeln beskriver IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.
- Azure IoT Edge funktioner i IoT Central.
- Så här ansluter du dina Azure IoT Edge runtime-baserade enheter till ditt program.

## <a name="known-issues"></a>Kända problem

> [!Note]
> Dessa kända problem gäller endast för IoT Central för hands versions program.

- Kontinuerlig data export har inte stöd för Avro-formatet (inkompatibilitet).
- Interjson stöds inte för närvarande.
- Kart panelen stöds inte för närvarande.
- Jobb stöder inte komplexa typer.
- Mat ris schema typer stöds inte.
- Endast C-enhetens SDK och Node. js-enhet och tjänst-SDK: er stöds.
- Den är endast tillgänglig på platser med USA och Europa.
- Enhets kapacitets modeller måste ha alla gränssnitt definierade infogade i samma fil.

## <a name="personas"></a>Profiler

IoT Central-dokumentationen avser fyra personer som interagerar med ett IoT Central-program:

- En _Solution Builder_ ansvarar för att definiera de typer av enheter som ansluter till programmet och anpassar programmet för-operatorn.
- en _operatör_ hanterar de enheter som är anslutna till programmet
- En _administratör_ ansvarar för administrativa uppgifter, till exempel hantering av [användar roller och behörigheter](howto-administer.md) i programmet.
- En _enhets utvecklare_ skapar den kod som körs på en enhet eller IoT Edge modul som är ansluten till ditt program.

## <a name="create-your-iot-central-application"></a>Skapa ditt IoT Central program

Som Solution Builder använder du IoT Central för att skapa en anpassad IoT-lösning för molnet som är värd för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt IoT Central-program och sedan anpassa det efter dina specifika krav i webbläsaren. Som Solution Builder använder du webbaserade verktyg för att skapa en _enhets mall_ för de enheter som ansluter till ditt program. En enhets mall är den skiss som definierar egenskaperna och beteendet för en typ av enhet, till exempel:

- Telemetri som skickas.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Egenskaper, som en operator anger, som avgör enhetens beteende.

Den här enhets mal len innehåller:

- En _modell för enhets kapacitet_ som beskriver de funktioner som en enhet ska implementera, t. ex. telemetri som skickas och de egenskaper som den rapporterar.
- Moln egenskaper som inte lagras på enheten.
- Anpassningar, instrument paneler och formulär som är en del av ditt IoT Central-program.

### <a name="pricing"></a>Priser

Du kan skapa IoT Central program med en kostnads fri utvärderings version på 7 dagar eller använda en standard prenumeration.

- Program som du skapar med hjälp av den **kostnads fria** planen är kostnads fria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standard pris plan när som helst innan de upphör att gälla.
- Program som du skapar med **standard** planen debiteras per enhet. du kan välja standard pris avtal för **standard 1** eller **Standard 2** med de två första enheterna som är kostnads fria. Läs mer om kostnads fria priser och standard priser på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).


### <a name="create-device-templates"></a>Skapa mallar för enheter

[IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) gör det möjligt för IoT Central att integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play är det ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central för hands versions program använder enhets mallarna de här IoT-Plug and Play enhets kapacitets modeller.

Som Solution Builder har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och implementera sedan dess enhets kapacitets modell i enhets koden.
- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat) och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen och Anslut enheten till IoT Central-programmet. IoT Central hittar enhetens kapacitets modell från en lagrings plats och skapar en enkel enhets mall åt dig.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till ditt IoT Central program och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.

Som Solution Builder kan du använda IoT Central för att generera kod för test enheter för att validera dina enhets mallar.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Som Solution Builder kan du också anpassa IoT Central programmets användar gränssnitt för de operatörer som ansvarar för den dagliga användningen av programmet. Anpassningar som en Solution Builder kan göra är:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="connect-your-devices"></a>Anslut dina enheter

När byggaren har definierat vilka typer av enheter som kan ansluta till programmet, skapar en enhetsutvecklare koden som ska köras på enheterna. Som enhetsutvecklare kan du använda den öppna källkoden [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) från Microsoft när du skapar din enhetskod. Dessa SDK: er har brett stöd för språk, plattform och protokoll för att möta dina behov för att ansluta dina enheter till ditt IoT Central-program. Med SDK: er kan du implementera följande enhets funktioner:

- skapa en säker anslutning
- skicka telemetri
- rapportera status
- ta emot konfigurationsuppdateringar.

Mer information finns i bloggposten om [fördelar med att använda Azure IoT-SDK:er och fallgropar att undvika om du inte gör det](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-enheter

Enheter som har skapats med [Azure IoT-SDK](https://github.com/Azure/azure-iot-sdks): er kan också ansluta [Azure IoT Edge enheter](../../iot-edge/about-iot-edge.md) till ett IoT Central-program. Med Azure IoT Edge kan du köra Cloud Intelligence och anpassad logik direkt på IoT-enheter som hanteras av IoT Central. Med IoT Edge runtime kan du:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

Mer information finns i [Azure IoT Edge enheter och IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Hantera ditt program

IoT Central program är helt värdar för Microsoft, vilket minskar administrations kostnaderna för att hantera dina program.

Som operatör använder du IoT Central-programmet för att hantera enheterna i din IoT Central-lösning. Operatörer utför uppgifter som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Som Solution Builder kan du definiera anpassade regler och åtgärder som körs via data strömning från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Administratörer hanterar åtkomst till ditt program med [användar roller och behörigheter](howto-administer.md).

## <a name="quotas"></a>Kvoter

Varje Azure-prenumeration har standard kvoter som kan påverka omfånget för din IoT-lösning. IoT Central begränsar för närvarande antalet program som du kan distribuera i en prenumeration på 10. Kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/)om du behöver öka den här gränsen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Central, föreslås följande steg:

- Förstå tillgängliga [Azure-tekniker och-tjänster för att skapa IoT-lösningar](../../iot-fundamentals/iot-services-and-technologies.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- Lär dig mer om [IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Lär dig hur du [skapar Azure IoT Edge enhets mal len](./tutorial-define-edge-device-type.md)
