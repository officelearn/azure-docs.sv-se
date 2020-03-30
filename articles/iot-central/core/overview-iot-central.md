---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som förenklar skapandet av IoT-lösningar och hjälper till att minska bördan och kostnaden för IoT-hanteringsåtgärder och utveckling. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 6c50e5892aca37bac47e68a9ae17435611c99817
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80157474"
---
# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

IoT Central är en IoT-applikationsplattform som minskar bördan och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företagsklass. Om du väljer att bygga med IoT Central kan du fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt föränderlig IoT-infrastruktur.

Med webbgränssnittet kan du övervaka enhetsvillkor, skapa regler och hantera miljontals enheter och deras data under hela livscykeln. Dessutom gör det att du kan agera på enhetsinsikter genom att utöka IoT-intelligens till affärsprogram.

Den här artikeln beskriver, för IoT Central:

- de vanligaste profilerna som är associerade med ett projekt
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.
- Azure IoT Edge-funktioner i IoT Central.
- Så här ansluter du dina Azure IoT Edge-körningsdrivna enheter till ditt program.

## <a name="known-issues"></a>Kända problem

- Kontinuerlig dataexport stöder inte Avro-formatet (inkompatibilitet).
- GeoJSON stöds för närvarande inte.
- Kartpanelen stöds för närvarande inte.
- Jobb stöder inte komplexa typer.
- Matrisschematyper stöds inte.
- Endast C-enheten SDK och Node.js-enheten och tjänsten SDK:er stöds.
- IoT Central är för närvarande tillgängligt i USA, Europa, Asien och Stillahavsområdet, Australien, Storbritannien och Japan.
- Du kan inte använda programmallen **För anpassat program (äldre)** på platsera i Storbritannien och Japan.
- Enhetskapacitetsmodeller måste ha alla gränssnitt definierade infogade i samma fil.
- Stöd för [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) är i förhandsversion och stöds endast i valda områden.

## <a name="personas"></a>Profiler

IoT Central-dokumentationen avser fyra personer som interagerar med ett IoT Central-program:

- En _lösningsbyggare_ ansvarar för att definiera de typer av enheter som ansluter till programmet och anpassa programmet för operatören.
- en _operatör_ hanterar de enheter som är anslutna till programmet
- En _administratör_ ansvarar för administrativa uppgifter som att hantera [användarroller och behörigheter](howto-administer.md) i programmet.
- En _enhetsutvecklare_ skapar koden som körs på en enhet eller IoT Edge-modul som är ansluten till ditt program.

## <a name="create-your-iot-central-application"></a>Skapa ditt IoT Central-program

Som lösningsbyggare använder du IoT Central för att skapa en anpassad, molnbaserad IoT-lösning för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt IoT Central-program och sedan anpassa det till dina specifika krav i din webbläsare. Du kan börja med en allmän _programmall_ eller med en av de branschfokuserade programmallarna för [Detaljhandel,](../retail/overview-iot-central-retail-pnp.md) [Energi,](../energy/overview-iot-central-energy.md) [Myndigheter](../government/overview-iot-central-government.md)eller [Sjukvård](../healthcare/overview-iot-central-healthcare.md).

Som lösningsbyggare använder du de webbaserade verktygen för att skapa en _enhetsmall_ för de enheter som ansluter till ditt program. En enhetsmall är skissen som definierar egenskaperna och beteendet hos en typ av enhet, till exempel:

- Telemetri det skickar.
- företagsegenskaper som en operatör kan ändra
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet.
- Egenskaper som en operator anger, som avgör enhetens beteende.

Den här enhetsmallen innehåller:

- En _enhetskapacitetsmodell_ som beskriver de funktioner som en enhet bör implementera, till exempel den telemetri som skickas och de egenskaper som den rapporterar.
- Molnegenskaper som inte lagras på enheten.
- Anpassningar, instrumentpaneler och formulär som ingår i ditt IoT Central-program.

### <a name="pricing"></a>Prissättning

Du kan skapa IoT Central-program med en 7-dagars kostnadsfri utvärderingsversion eller använda en standardprisplan.

- Program som du skapar med den *kostnadsfria* planen är gratis i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem för att använda en standardprisplan när som helst innan de upphör att gälla.
- Program som du skapar med hjälp av en *standardplan* faktureras per enhet, du kan välja antingen **standard 1-** eller **standard 2-prisplan** när de två första enheterna är gratis. Läs mer om de kostnadsfria och standardiserade prisplanerna på [prissidan för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Skapa enhetsmallar

[IoT Plug and Play (förhandsgranskning)](../../iot-pnp/overview-iot-plug-and-play.md) gör det möjligt för IoT Central att integrera enheter utan att du skriver någon inbäddad enhetskod. Kärnan i IoT Plug and Play (förhandsversion) är ett modellschema för enhetskapacitet som beskriver enhetsfunktioner. I ett IoT Central-program använder enhetsmallar dessa IoT Plug and Play-modeller (preview).

Som lösningsbyggare har du flera alternativ för att skapa enhetsmallar:

- Importera en enhetskapacitetsmodell från [Azure Certified for IoT-enhetskatalogen](https://aka.ms/iotdevcat) och lägg sedan till alla molnegenskaper, anpassningar och instrumentpaneler som ditt IoT Central-program behöver.
- Utforma enhetsmallen i IoT Central och implementera sedan dess enhetskapacitetsmodell i enhetskoden.
- Skapa en enhetskapacitetsmodell med Visual Studio-kod och publicera modellen i en databas. Implementera enhetskoden från modellen och anslut enheten till ditt IoT Central-program. IoT Central hittar enhetskapacitetsmodellen från databasen och skapar en enkel enhetsmall åt dig.
- Skapa en enhetskapacitetsmodell med Visual Studio-kod. Implementera enhetskoden från modellen. Importera enhetskapacitetsmodellen manuellt till ditt IoT Central-program och lägg sedan till molnegenskaper, anpassningar och instrumentpaneler som ditt IoT Central-program behöver.

Som lösningsbyggare kan du använda IoT Central för att generera kod för testenheter för att validera enhetsmallarna.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Som lösningsbyggare kan du också anpassa IoT Central-programgränssnittet för de operatörer som ansvarar för den dagliga användningen av programmet. Anpassningar som en lösningsbyggare kan göra är:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="pricing"></a>Prissättning

Du kan skapa IoT Central-program med en 7-dagars kostnadsfri utvärderingsversion eller använda en standardprisplan.

- Program som du skapar med den *kostnadsfria* planen är gratis i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem för att använda en standardprisplan när som helst innan de upphör att gälla.
- Program som du skapar med *standardplanen* faktureras per enhet, du kan välja antingen **standard 1-** eller **standard 2-prisplan** när de två första enheterna är gratis. Läs mer om [IoT Central prissättning](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Anslut dina enheter

Azure IoT Central använder [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) för att hantera all enhetsregistrering och anslutning.

Med HJÄLP AV DPS kan:

- IoT Central för att stödja introduktions- och anslutningsenheter i stor skala.
- Du kan generera enhetsautentiseringsuppgifter och konfigurera enheterna offline utan att registrera enheterna via IoT Central UI.
- Enheter som ska anslutas med signaturer för delad åtkomst.
- Enheter som ska anslutas med X.509-certifikat av branschstandard.
- Du använder dina egna enhets-ID:er för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID:er förenklas integreringen med befintliga backoffice-system.
- Ett enda konsekvent sätt att ansluta enheter till IoT Central.

Mer information finns i [Ansluta till Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-enheter

Förutom enheter som skapats med [Azure IoT SDK:er](https://github.com/Azure/azure-iot-sdks)kan du även ansluta [Azure IoT Edge-enheter](../../iot-edge/about-iot-edge.md) till ett IoT Central-program. Med Azure IoT Edge kan du köra molninformation och anpassad logik direkt på IoT-enheter som hanteras av IoT Central. Med IoT Edge-körningen kan du:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

Mer information finns i [Azure IoT Edge-enheter och IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Fortsätt vara uppkopplad

IoT Central-program är helt värd Microsoft, vilket minskar administrationen overhead för att hantera dina program.

Som operatör använder du IoT Central-programmet för att hantera enheterna i din IoT Central-lösning. Operatörer gör uppgifter som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Som lösningsbyggare kan du definiera anpassade regler och åtgärder som fungerar över dataströmning från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Administratörer hanterar åtkomsten till ditt program med [användarroller och behörigheter](howto-administer.md).

Med alla IoT-lösningar som utformats för att fungera i stor skala är en strukturerad metod för enhetshantering viktig. Det räcker inte att bara ansluta dina enheter till molnet, du måste hålla dina enheter anslutna och friska. En operatör kan använda följande IoT Central-funktioner för att hantera dina enheter under hela programmets livscykel:

### <a name="dashboards"></a>Instrumentpaneler 

Inbyggda [instrumentpaneler](./howto-set-up-template.md#generate-default-views) ger ett anpassningsbart användargränssnitt för att övervaka enhetens hälsa och telemetri. Börja med en förbyggd instrumentpanel i en [programmall](howto-use-app-templates.md) eller skapa egna instrumentpaneler som är anpassade efter dina operatörers behov. Du kan dela instrumentpaneler med alla användare i programmet eller hålla dem privata.

### <a name="rules-and-actions"></a>Regler och åtgärder 

Skapa [anpassade regler](tutorial-create-telemetry-rules.md) baserat på enhetstillstånd och telemetri för att identifiera enheter som behöver åtgärdas. Konfigurera åtgärder för att meddela rätt personer och se till att korrigerande åtgärder vidtas i tid.

### <a name="jobs"></a>Jobb 

[Med jobb](howto-run-a-job.md) kan du använda enskilda uppdateringar eller massuppdateringar på enheter genom att ange egenskaper eller anropa kommandon. 

### <a name="user-roles-and-permissions"></a>Användarroller och behörigheter

[Med roller och behörigheter](howto-manage-users-roles.md) kan en administratör skräddarsy varje användares upplevelse. En administratör använder webbgränssnittet för att skapa roller och tilldela behörigheter.

## <a name="transform-your-iot-data"></a>Omvandla dina IoT-data

Som en programplattform kan du med IoT Central omvandla dina IoT-data till affärsinsikter som driver användbara resultat. [Regler,](./tutorial-create-telemetry-rules.md) [dataexport](./howto-export-data.md)och [det offentliga REST API:et](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) är exempel på hur du kan integrera IoT Central med affärsspecifika program:

![Hur IoT Central kan omvandla dina IoT-data](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Övervaka enhetens hälsa och åtgärder med hjälp av regler

När dina enheter är anslutna och skickar data kan regler identifiera enheter som har problem eller skicka felmeddelanden så att du kan åtgärda dem med minimal stilleståndstid. Skapa regler i ditt IoT Central-program för att övervaka telemetri från dina enheter och varna en operatör när ett mått överskrider ett tröskelvärde eller en enhet skickar ett visst meddelande. E-poståtgärder och webhooks för dina regler meddela rätt personer och rätt nedströms system.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Kör anpassad analys och bearbetning på dina exporterade data

Du kan generera affärsinsikter, till exempel bestämma trender för maskineffektivitet eller förutsäga framtida energiförbrukning på ett fabriksgolv, genom att skapa anpassade analyspipelpipelsar för att bearbeta telemetri från dina enheter och lagra resultaten. Konfigurera dataexport i IoT Central-programmet för att exportera telemetri, enhetsegenskapsändringar och enhetsmalländringar till andra tjänster där du kan analysera, lagra och visualisera data med de verktyg du föredrar.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Skapa anpassade IoT-lösningar och integrationer med REST-API:erna

Bygg IoT-lösningar som:

- Appar med mobila följeslagare som kan fjärrinställda och styra enheter.
- Anpassade integreringar som gör det möjligt för befintliga affärsprogram att interagera med dina IoT-enheter och data.
- Enhetshanteringsprogram för enhetsmodellering, introduktion, hantering och dataåtkomst.

## <a name="quotas"></a>Kvoter

Varje Azure-prenumeration har standardkvoter som kan påverka omfattningen av din IoT-lösning. För närvarande begränsar IoT Central antalet program som du kan distribuera i en prenumeration till 10. Om du behöver öka den här gränsen kontaktar du [Microsoft-supporten](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Central föreslås följande steg:

- Förstå tillgängliga [Azure-tekniker och tjänster för att skapa IoT-lösningar](../../iot-fundamentals/iot-services-and-technologies.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- Läs mer om [IoT Plug and Play (förhandsgranskning)](../../iot-pnp/overview-iot-plug-and-play.md).
- Lär dig hur du [ansluter en Azure IoT Edge-enhet](./tutorial-add-edge-as-leaf-device.md).
- Läs mer om [Azure IoT-tekniker och -tjänster](../../iot-fundamentals/iot-services-and-technologies.md).
