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
ms.openlocfilehash: 05d73ae09d95879c37e86f5f3e73e35ed4607296
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82560968"
---
# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

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

- Kontinuerlig data export har inte stöd för Avro-formatet (inkompatibilitet).
- Interjson stöds inte för närvarande.
- Kart panelen stöds inte för närvarande.
- Jobb stöder inte komplexa typer.
- Mat ris schema typer stöds inte.
- Endast C-enhetens SDK och Node. js-enhet och tjänst-SDK: er stöds.
- IoT Central är för närvarande tillgängligt på platserna USA, Europa, Asien och stillahavsområdet, Australien, Storbritannien och Japan.
- Du kan inte använda program mal len **anpassad tillämpning (bakåtkompatibelt)** på platser i Storbritannien och Japan.
- Enhets kapacitets modeller måste ha alla gränssnitt definierade infogade i samma fil.
- Stöd för [IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) är i för hands version och endast stöds i valda regioner.

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

Du kan snabbt distribuera ett nytt IoT Central-program och sedan anpassa det efter dina specifika krav i webbläsaren. Du kan börja med en allmän _Programmall_ eller med en av de branschspecifika Programmallarna för [detalj handel](../retail/overview-iot-central-retail.md), [energi](../energy/overview-iot-central-energy.md), [myndigheter](../government/overview-iot-central-government.md)eller [hälso vård](../healthcare/overview-iot-central-healthcare.md).

Som Solution Builder använder du webbaserade verktyg för att skapa en _enhets mall_ för de enheter som ansluter till ditt program. En enhets mall är den skiss som definierar egenskaperna och beteendet för en typ av enhet, till exempel:

- Telemetri som skickas.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Egenskaper, som en operator anger, som avgör enhetens beteende.

Den här enhets mal len innehåller:

- En _modell för enhets kapacitet_ som beskriver de funktioner som en enhet ska implementera, t. ex. telemetri som skickas och de egenskaper som den rapporterar.
- Moln egenskaper som inte lagras på enheten.
- Anpassningar, instrument paneler och formulär som är en del av ditt IoT Central-program.

### <a name="pricing"></a>Prissättning

Du kan skapa IoT Central program med en kostnads fri utvärderings version på 7 dagar eller använda en standard pris plan.

- Program som du skapar med hjälp av den *kostnads fria* planen är kostnads fria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standard pris plan när som helst innan de upphör att gälla.
- Program som du skapar med en *standard* -plan debiteras per enhet. du kan välja standard pris Planen för **standard 1** eller **Standard 2** med de två första enheterna som är kostnads fria. Läs mer om kostnads fria priser och standard priser på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Skapa mallar för enheter

[IoT plug and Play (för hands version)](../../iot-pnp/overview-iot-plug-and-play.md) gör att IoT Central integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play (för hands version) är ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central-program använder Device Template dessa modeller av enhets kapacitet för IoT Plug and Play (för hands version).

Som Solution Builder har du flera alternativ för att skapa enhets mallar:

- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat) och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Utforma enhets mal len i IoT Central och implementera sedan dess enhets kapacitets modell i enhets koden.
- Skapa en enhets kapacitets modell med Visual Studio Code och publicera modellen till en lagrings plats. Implementera din enhets kod från modellen och Anslut enheten till IoT Central-programmet. IoT Central hittar enhetens kapacitets modell från lagrings platsen och skapar en enkel enhets mall åt dig.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till ditt IoT Central program och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.

Som Solution Builder kan du använda IoT Central för att generera kod för test enheter för att validera dina enhets mallar.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Som Solution Builder kan du också anpassa IoT Central programmets användar gränssnitt för de operatörer som ansvarar för den dagliga användningen av programmet. Anpassningar som en Solution Builder kan göra är:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="pricing"></a>Prissättning

Du kan skapa IoT Central program med en kostnads fri utvärderings version på 7 dagar eller använda en standard pris plan.

- Program som du skapar med hjälp av den *kostnads fria* planen är kostnads fria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standard pris plan när som helst innan de upphör att gälla.
- Program som du skapar med *standard* planen debiteras per enhet. du kan välja standard pris avtal för **standard 1** eller **Standard 2** med de två första enheterna som är kostnads fria. Läs mer om [IoT Central prissättning](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Anslut dina enheter

Azure IoT Central använder [azure IoT Hub Device Provisioning service (DPS)](../../iot-dps/about-iot-dps.md) för att hantera all enhets registrering och anslutning.

Genom att använda DPS kan du:

- IoT Central för att stödja onboarding och ansluta enheter i stor skala.
- Du genererar autentiseringsuppgifter för enheten och konfigurerar enheterna offline utan att registrera enheterna via IoT Central användar gränssnitt.
- Enheter som ska anslutas med signaturer för delad åtkomst.
- Enheter som ska anslutas med bransch standard X. 509-certifikat.
- Du kan använda dina egna enhets-ID: n för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID: er blir det enklare att integrera med befintliga Back Office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

Mer information finns i Anslut [till Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-enheter

Enheter som har skapats med [Azure IoT-SDK](https://github.com/Azure/azure-iot-sdks): er kan också ansluta [Azure IoT Edge enheter](../../iot-edge/about-iot-edge.md) till ett IoT Central-program. Med Azure IoT Edge kan du köra Cloud Intelligence och anpassad logik direkt på IoT-enheter som hanteras av IoT Central. Med IoT Edge runtime kan du:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

Mer information finns i [Azure IoT Edge enheter och IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Fortsätt vara uppkopplad

IoT Central program är helt värdar för Microsoft, vilket minskar administrations kostnaderna för att hantera dina program.

Som operatör använder du IoT Central-programmet för att hantera enheterna i din IoT Central-lösning. Operatörer utför uppgifter som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Som Solution Builder kan du definiera anpassade regler och åtgärder som körs via data strömning från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Administratörer hanterar åtkomst till ditt program med [användar roller och behörigheter](howto-administer.md).

Med en IoT-lösning som är utformad för att användas i stor skala är det viktigt med en strukturerad metod för enhets hantering. Det räcker inte bara att ansluta dina enheter till molnet, du måste hålla enheterna anslutna och felfria. En operatör kan använda följande IoT Central funktioner för att hantera dina enheter under hela programmets livs cykel:

### <a name="dashboards"></a>Instrumentpaneler 

Inbyggda [instrument paneler](./howto-set-up-template.md#generate-default-views) ger ett anpassningsbart gränssnitt för övervakning av enhetens hälso tillstånd och telemetri. Börja med en fördefinierad instrument panel i en [Programmall](howto-use-app-templates.md) eller skapa egna instrument paneler som är anpassade efter dina operatörers behov. Du kan dela instrument paneler med alla användare i ditt program eller hålla dem privata.

### <a name="rules-and-actions"></a>Regler och åtgärder

Bygg [anpassade regler](tutorial-create-telemetry-rules.md) baserat på enhetens tillstånd och telemetri för att identifiera vilka enheter som behöver åtgärdas. Konfigurera åtgärder för att meddela rätt personer och se till att korrigerande åtgärder vidtas i rätt tid.

### <a name="jobs"></a>Jobb

Med [jobb](howto-run-a-job.md) kan du tillämpa enstaka eller Mass uppdateringar av enheter genom att ange egenskaper eller anropa kommandon. 

### <a name="user-roles-and-permissions"></a>Användar roller och behörigheter

[Roller och behörigheter](howto-manage-users-roles.md) låter en administratör skräddarsy varje användares upplevelse. En administratör använder webb gränssnittet för att skapa roller och tilldela behörigheter.

## <a name="transform-your-iot-data"></a>Transformera dina IoT-data

Som en program plattform kan IoT Central omvandla dina IoT-data till affärs insikter som enhets åtgärds resultat. [Regler](./tutorial-create-telemetry-rules.md), [data export](./howto-export-data.md)och de [offentliga REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) är exempel på hur du kan integrera IoT Central med branschspecifika program:

![Hur IoT Central kan transformera dina IoT-data](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Övervaka enhetens hälso tillstånd och åtgärder med hjälp av regler

När dina enheter är anslutna och skickar data kan regler identifiera enheter som har problem eller skicka fel meddelanden så att du kan åtgärda dem med minimal stillestånds tid. Bygg regler i IoT Central program för att övervaka telemetri från dina enheter och varna en operatör när ett mått överkorsar ett tröskelvärde eller en enhet skickar ett särskilt meddelande. E-poståtgärder och webhookar för dina regler meddelar rätt personer och rätt underordnade system.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Kör anpassad analys och bearbetning på dina exporterade data

Du kan generera affärs insikter, till exempel för att fastställa maskin effektivitets trender eller förutsäga framtida energi förbrukning på en fabriks plan, genom att skapa anpassade analys pipelines för att bearbeta telemetri från dina enheter och lagra resultatet. Konfigurera data exporter i ditt IoT Central program för att exportera telemetri, ändringar av enhets egenskaper och enhets mal len ändras till andra tjänster där du kan analysera, lagra och visualisera data med dina önskade verktyg.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Bygg anpassade IoT-lösningar och integreringar med REST-API: er

Bygg IoT-lösningar som:

- Mobile Companion-appar som kan fjärrkonfigurera och kontrol lera enheter.
- Anpassade integreringar som gör det möjligt för befintliga branschspecifika program att interagera med dina IoT-enheter och-data.
- Enhets hanterings program för enhets modellering, onboarding, hantering och data åtkomst.

## <a name="quotas"></a>Kvoter

Varje Azure-prenumeration har standard kvoter som kan påverka omfånget för din IoT-lösning. IoT Central begränsar för närvarande antalet program som du kan distribuera i en prenumeration på 10. Kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/)om du behöver öka den här gränsen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Central finns några förslag på nästa steg:

- Förstå tillgängliga [Azure-tekniker och-tjänster för att skapa IoT-lösningar](../../iot-fundamentals/iot-services-and-technologies.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- Lär dig mer om [IoT plug and Play (för hands version)](../../iot-pnp/overview-iot-plug-and-play.md).
- Lär dig hur du [ansluter en Azure IoT Edge enhet](./tutorial-add-edge-as-leaf-device.md).
- Lär dig mer om [Azure IoT-tekniker och-tjänster](../../iot-fundamentals/iot-services-and-technologies.md).

Om du är en enhets utvecklare och vill gå in i viss kod, är det föreslagna nästa steg att [skapa och ansluta ett klient program till ditt Azure IoT Central-program](./tutorial-connect-device-nodejs.md).
