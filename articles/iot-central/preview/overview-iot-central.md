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
ms.openlocfilehash: 1269743d6c8354054a17b94d3470800a94180090
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893867"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Vad är Azure IoT Central (för hands versions funktioner)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> [IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) -funktionerna i Azure IoT Central finns för närvarande i en offentlig för hands version. Använd inte ett IoT-Plug and Play aktiverat IoT Central program för produktions arbets belastningar. För produktions miljöer används ett IoT Central-program som skapats från en aktuell, allmänt tillgänglig program mal len.

Azure IoT Central är en IoT-programplattform som minskar belastningen och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företags klass. Genom att välja att skapa med Azure IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

Med webb gränssnittet kan du övervaka enhets villkor, skapa regler och hantera miljon tals enheter och deras data under deras livs cykel. Dessutom kan du agera på enhets insikter genom att utöka IoT Intelligence till branschspecifika program.

Den här artikeln beskriver för Azure IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.
- Översikt över IoT Edge funktioner i IoT Central
- Så här ansluter du dina Azure IoT Edge runtime-baserade enheter till ditt program.


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
- Den är endast tillgänglig i regionerna Nord Europa och USA, centrala.
- Enhets kapacitets modeller måste ha alla gränssnitt definierade infogade i samma fil.

## <a name="personas"></a>Profiler

Azure IoT Central-dokumentationen avser fyra personer som interagerar med ett Azure IoT Central-program:

- En _Solution Builder_ ansvarar för att definiera de typer av enheter som ansluter till programmet och anpassar programmet för-operatorn.
- en _operatör_ hanterar de enheter som är anslutna till programmet
- En _administratör_ ansvarar för administrativa uppgifter, till exempel hantering av [användar roller och behörigheter](howto-administer.md) i programmet.
- en _enhetsutvecklare_ skapar den kod som körs på en enhet som är ansluten till ditt program.
- En _enhet/modul-utvecklare_ skapar koden/modulen som körs på en enhet som är ansluten till ditt program.

## <a name="create-your-azure-iot-central-application"></a>Skapa ditt Azure IoT Central-program

Som byggare använder du Azure IoT Central till att skapa en anpassad, molnbaserad IoT-lösning för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt Azure IoT Central-program och sedan anpassa det efter dina specifika krav i webbläsaren. Som ett verktyg använder du webbaserade verktyg för att skapa en _enhets mall_ för de enheter som ansluter till ditt program. En enhets mall är den skiss som definierar egenskaperna och beteendet för en typ av enhet, till exempel:

- Telemetri som skickas.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Egenskaper, som en operator anger, som avgör enhetens beteende.

Den här enhets mal len innehåller:

- En _modell för enhets kapacitet_ som beskriver de funktioner som en enhet ska implementera, t. ex. telemetri som skickas och de egenskaper som den rapporterar.
- Moln egenskaper som inte lagras på enheten.
- Anpassningar, instrument paneler och formulär som är en del av ditt IoT Central-program.

### <a name="create-device-templates"></a>Skapa mallar för enheter

[IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) gör det möjligt för IoT Central att integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play är det ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central för hands versions program använder enhets mallarna de här IoT-Plug and Play enhets kapacitets modeller.

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

Administratörer hanterar åtkomst till ditt program med [användar roller och behörigheter](howto-administer.md).


## <a name="what-is-azure-iot-central-with-azure-iot-edge-preview-features"></a>Vad är Azure IoT Central med Azure IoT Edge (förhands gransknings funktioner)

IoT Central utökar sin portfölj genom att stödja Azure IoT Edge enheter. 

Nu kan företag köra Cloud Intelligence direkt på IoT-enheter som hanteras av Azure IoT Central. Den här nya funktionen hjälper företag att ansluta och hantera Azure IoT Edge enheter som kör Azure IoT Edge runtime, distribuera programmoduler, publicera insikter och vidta åtgärder i skala – allt från IoT Central. 

[Översikt över Azure IoT Edge](../../iot-edge/about-iot-edge.md)

### <a name="overview-of-iot-edge-capabilities-in-iot-central"></a>Översikt över IoT Edge funktioner i IoT Central

Azure IoT Edge-körning möjliggör anpassad och molnbaserad logik på IoT Edge-enheter. IoT Edge enheten drivs av körningen och utför hanterings-och kommunikations åtgärder. 

Azure IoT Edge runtime utför följande funktioner:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

![IoT Central med Azure IoT Edge översikt](./media/overview-iot-central/iotedge.png)

Azure IoT Central utför följande funktioner: 

- Stöd för Azure IoT Edge enhets mal len som beskriver de funktioner som en Azure IoT Edge enhet bör implementera som 
  1. överförings funktion för distributions manifest, som hjälper dig att hantera ett manifest för en flotta av enheter
  2. moduler som ska köras på den Azure IoT Edge enheten
  3. telemetri varje modul skickar
  4. egenskaper för varje modul-rapport och 
  5. kommandot svarar på varje modul
  6. Upprätta relationer mellan Azure IoT Edge Gateway enhets kapacitets modell och kapacitets modell för underordnad enhet
  7. Moln egenskaper som inte lagras på den Azure IoT Edge enheten
  8. Anpassningar, instrument paneler och formulär som ingår i ditt IoT Central program

  [Skapa Azure IoT Edge enhets mall](./tutorial-define-edge-device-type.md)
   
- Etablering Azure IoT Edge enheter i stor skala med Azure IoT Device Provisioning-tjänsten
- Utlös regler och vidta åtgärder på Azure IoT Edge enheter
- Bygg instrument paneler och analys 
- Kontinuerlig data export av telemetri som flödar från Azure IoT Edge enheter

### <a name="azure-iot-edge-device-types-in-iot-central"></a>Azure IoT Edge enhets typer i IoT Central

Azure IoT Central klassificerar Azure IoT Edge enhets typer på följande sätt:

- Azure IoT Edge enhet som en löv enhet. Azure IoT Edge enheten kan ha underordnade enheter, men underordnade enheter är inte etablerade i IoT Central
- Azure IoT Edge enhet som en gateway-enhet med underordnade enheter. Både gateway-enheter och underordnade enheter tillhandahålls i IoT Central

![IoT Central med Azure IoT Edge översikt](./media/overview-iot-central/gatewayedge.png)

### <a name="azure-iot-edge-patterns-supported-in-iot-central"></a>Azure IoT Edge mönster som stöds i IoT Central

- Azure IoT Edge som löv enhets ![Azure IoT Edge som löv enhet](./media/overview-iot-central/edgeasleafdevice.png) Azure IoT Edge enheten kommer att tillhandahållas i IoT Central och eventuella efterföljande enheter och dess telemetri kommer att visas som kommer från Azure IoT Edge enhet. Underordnade enheter om någon som är ansluten till Azure IoT Edge-enheten inte är etablerad i IoT Central. 

- Azure IoT Edge gateway-enhet som är ansluten till underordnade enheter med identitets ![Azure IoT Edge med underordnad enhets identitet](./media/overview-iot-central/edgewithdownstreamdeviceidentity.png) Azure IoT Edge enheten kommer att tillhandahållas i IoT Central tillsammans med de efterföljande enheterna som är anslutna till Azure IoT Edge-enheten. Körnings stöd för etablering av underordnade enheter via gateway planeras i framtiden. IoT Central kommer att kontrol säga upp molnets första etablering av de underordnade enheterna och autentiseringsuppgifterna hanteras manuellt på den underordnade enheten. Enhets första etablering av underordnade enheter planerade för framtida terminer. 

- Azure IoT Edge gateway-enhet som är ansluten till underordnade enheter med identiteten som tillhandahålls av Edge Gateway-![Azure IoT Edge med underordnad enhet utan identitet](./media/overview-iot-central/edgewithoutdownstreamdeviceidentity.png) Azure IoT Edge-enheten kommer att tillhandahållas i IoT Central tillsammans med de efterföljande enheterna som är anslutna till Azure IoT Edge-enheten. Stöd för körning av gateway som ger identitet till underordnade enheter och etablering av underordnade enheter planeras i framtiden. Du kan ta med din egen identitet för identitets översättning och IoT Central kommer att stödja det här mönstret. 

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central föreslår vi nästa steg:

- Förstå skillnaderna mellan [Azure IoT Central och Azure IoT-lösningsacceleratorer](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- Lär dig mer om [IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Lär dig hur du [skapar Azure IoT Edge enhets mal len](./tutorial-define-edge-device-type.md)
