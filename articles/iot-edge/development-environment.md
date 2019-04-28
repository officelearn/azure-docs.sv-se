---
title: Azure IoT Edge-utvecklingsmiljö | Microsoft Docs
description: Lär dig mer om de system som stöds och från första part utvecklingsverktyg som hjälper dig att skapa i IoT Edge-moduler
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3d1fb393138aeb590bc0645d6abe93652a52a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247564"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Förbereda utvecklings- och testmiljö för IoT Edge

Azure IoT Edge flyttar din befintliga affärslogik till enheter som fungerar på gränsen. Förbereda dina program och arbetsbelastningar som ska köras som [IoT Edge-moduler](iot-edge-modules.md), måste du skapa dem som behållare. Den här artikeln innehåller vägledning för hur du konfigurerar utvecklingsmiljön så att du kan skapa en IoT Edge-lösning. När du har utvecklingsmiljön och konfigurera sedan du kan lära dig hur du [utveckla dina egna IoT Edge-moduler](module-development.md).

Det finns minst två datorer att tänka på i någon IoT Edge-lösning. En är IoT Edge-enhet (eller enheter) själva, som kör IoT Edge-modul. Den andra är utvecklingsdator som används för att skapa, testa och distribuera moduler. Den här artikeln fokuserar främst på utvecklingsdator. I testsyfte kan de två datorerna vara detsamma. Du kan köra IoT Edge på en utvecklingsdator och distribuera moduler till den.

## <a name="operating-system"></a>Operativsystem

Azure IoT Edge som körs på en specifik uppsättning [operativsystem som stöds](support.md). För att utveckla IoT Edge, kan du använda de flesta operativsystem som kan köra en motor för behållaren. Behållare-motorn är ett krav på utvecklingsdator för att bygga dina moduler som behållare och överföra dem till ett behållarregister. 

Om din utvecklingsdator inte kan köra Azure IoT Edge, fortsätta i den här artikeln om du vill veta mer om [testverktyg](#testing-tools) som hjälper dig att testa och felsöka lokalt. 

Operativsystemet på utvecklingsdatorn behöver inte matcha operativsystemet på din IoT Edge-enhet. Operativsystemet behållare måste dock vara konsekvent mellan utvecklingssystemet och IoT Edge-enhet. Du kan till exempel utveckla moduler på en Windows-dator och distribuera dem till en Linux-enhet. Windows-datorn måste köra Linux-behållare för att skapa modulerna för Linux-enheten. 

## <a name="container-engine"></a>Motorn för behållare

Central begreppet IoT Edge är att du via fjärranslutning kan distribuera logik affärs- och molnet till enheter genom att paketera den i behållare. För att skapa behållare, behöver du en motor för behållaren på din utvecklingsdator. 

Endast stöds container-motor som IoT Edge-enheter i produktion är Moby. Dock kan en motor för behållare som är kompatibel med öppna Container-initiativet, som Docker, för att skapa IoT Edge-modul-avbildningar. 

## <a name="development-tools"></a>Utvecklingsverktyg

Både Visual Studio och Visual Studio Code har tillägget tillägg för att utveckla IoT Edge-lösningar. Dessa tillägg ger språkspecifika mallar för att skapa och distribuera nya IoT Edge-scenarier. Azure IoT Edge-tillägg för Visual Studio och Visual Studio Code hjälp du code, bygga, distribuera och felsöka dina IoT Edge-lösningar. Du kan skapa en hel IoT Edge-lösning som innehåller flera moduler och tillägg som uppdateras automatiskt en Distributionsmall för manifest med varje ny modul-tillägg. Med tillägg, kan du också hantera dina IoT-enheter i Visual Studio eller Visual Studio Code. Distribuerar moduler på en enhet, övervaka status och visa meddelanden när de anländer till IoT Hub. Båda tilläggen använder de [IoT EdgeHub utvecklingsverktyg](#iot-edgehub-dev-tool) att aktivera lokal körning och felsökning av moduler på din utvecklingsdator. 

Om du föredrar att utveckla med andra redigerare eller från CLI tillhandahåller Azure IoT Edge-utvecklingsverktyg kommandon så att du kan utveckla och testa från kommandoraden. Du kan skapa nya IoT-Edge scenarier, skapa modulen avbildningar, kör moduler i en simulator och övervaka meddelanden som skickas till IoT Hub. 

### <a name="visual-studio-code-extension"></a>Visual Studio Code-tillägg

Azure IoT Edge-tillägget för Visual Studio Code ger IoT Edge-modulen mallar som bygger på programmeringsspråk inklusive C, C#, Java, Node.js och Python, samt Azure functions i C#. 

Mer information och för att hämta Se [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Förutom IoT Edge-tillägg, kan det vara bra att installera ytterligare tillägg för att utveckla. Du kan till exempel använda [Docker-stöd för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) att hantera dina bilder, behållare och register. Dessutom har alla större språk tillägg för Visual Studio Code som kan vara till hjälp när du utvecklar moduler. 

#### <a name="prerequisites"></a>Nödvändiga komponenter

Modul-mallar för vissa språk och tjänster har förutsättningar som krävs för att bygga projektet mappar på en utvecklingsdator med Visual Studio Code.

| Modulen mall | Krav |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Ställa in miljövariabeln JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven 3.](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js-modulen generator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-2017-extension"></a>Tillägget för Visual Studio 2017

Azure IoT Edge-verktyg för Visual Studio tillhandahåller en IoT Edge modul-mall som bygger C#. 

Mer information och för att hämta Se [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge-utvecklingsverktyg

Azure IoT Edge-utvecklingsverktyg förenklar utvecklingen av IoT Edge med kommandoradsverktyget funktioner. Det här verktyget förser CLI-kommandon för att utveckla, felsöka och testa moduler. IoT Edge-utvecklingsverktyg som fungerar med utvecklingssystemet, oavsett om du har installerat manuellt beroenden på din dator eller använder behållaren IoT Edge-utveckling. 

Mer information och du kommer igång finns i [IoT Edge dev verktyget wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testverktyg

Det finns flera testverktyg för att hjälpa dig att simulera en IoT Edge-enheter eller felsöka moduler mer effektivt. I följande tabell visas en övergripande jämförelse mellan verktyg och sedan enskilda avsnitt beskrivs varje verktyg mer specifikt. 

Endast IoT Edge-körningen stöds för distributioner av produktion, men följande verktyg kan du simulera eller enkelt skapa IoT Edge-enheter för utveckling och testning. Dessa verktyg är inte ömsesidigt uteslutande, men kan fungera tillsammans för en komplett utvecklings-upplevelse. 

| Verktyg | Även känt som | Plattformar som stöds | Bäst för |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub utvecklingsverktyg  | iotedgehubdev | Windows, Linux, MacOS | Simulera en enhet för att felsöka moduler. |
| Behållare för IoT Edge-utveckling | Microsoft/iotedgedev | Windows, Linux, MacOS | Utveckla utan att installera beroenden. |
| IoT Edge-körning i en behållare | iotedgec | Windows, Linux, MacOS, ARM | Tester på en enhet som inte stöder körningen. |
| IoT Edge-enhetsbehållare | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testa ett scenario med många IoT Edge-enheter i stor skala. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub utvecklingsverktyg

Azure IoT EdgeHub utvecklingsverktyg är lokal utveckling och felsökning. Verktyget hjälper till att starta IoT Edge-moduler utan IoT Edge-körningen så att du kan skapa, utveckla, testa, köra och felsöka IoT Edge-moduler och lösningar lokalt. Du behöver skicka avbildningar till ett behållarregister och distribuera dem till en enhet för att testa.

Utvecklingsverktyg IoT EdgeHub har utformats för att fungera tillsammans med Visual Studio och Visual Studio Code, samt med IoT Edge-utvecklingsverktyg. Den stöder interna slingan utveckling samt yttre loop testning, så kan integreras med DevOps-verktyg för. 

Mer information och för att installera, se [utvecklingsverktyg för Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Behållare för IoT Edge-utveckling

Behållare för Azure IoT Edge-utveckling är en dockerbehållare som har alla beroenden som du behöver för utveckling av IoT Edge. Den här behållaren gör det enkelt att komma igång med oavsett vilket språk du vill utveckla i, inklusive C#, Python, Node.js och Java. Allt du behöver installera är en motor för behållaren, t.ex. Docker eller Moby, för att hämta behållaren till din utvecklingsdator. 

Mer information finns i [behållare för Azure IoT Edge-utveckling](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge-körning i en behållare

IoT Edge-körning i en behållare ger en fullständig runtime som tar enhetens anslutningssträng som en miljövariabel. Den här behållaren kan du testa IoT Edge-moduler och scenarier som inte stöder körningen internt, t.ex. MacOS. Alla moduler som du distribuerar kommer att startas utanför runtime-behållare. Om du vill att körningen och eventuella distribuerade moduler finns inom samma behållare kan du överväga att behållaren IoT Edge-enhet i stället.

Mer information finns i [som kör Azure IoT Edge i en behållare](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge-enhetsbehållare

Enhetsbehållare IoT Edge är en fullständig IoT Edge-enhet, redo att köras på valfri dator med en motor för behållare. Behållaren enhet innehåller IoT Edge-körningen och en behållare-motorn. Varje instans av behållaren är en helt funktionella Automatisk etablering IoT Edge-enhet. Behållaren enhet har stöd för fjärrfelsökning av moduler, så länge det finns en nätverksväg i modulen. Behållaren enhet är bra för att snabbt skapa stora mängder IoT Edge-enheter att testa scenarier i skala eller DevOps-pipelines. Det har också stöd för distribution till kubernetes via helm. 

Mer information finns i [Azure IoT Edge-enhetsbehållare](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-verktyg

När du är redo att utveckla i Skala lösningar för omfattande produktionsscenarion möjliga kan dra nytta av moderna DevOps-principer, inklusive automation, övervakning och effektiv programvaruutveckling processer. IoT Edge har tillägg för DevOps-verktyg som Azure DevOps, Azure DevOps-projekt och Jenkins. Om du vill anpassa en befintlig pipeline eller använda ett annat DevOps-verktyg som CircleCI eller TravisCI kan du göra det med CLI-funktioner som ingår i IoT Edge-utvecklingsverktyg.

Mer information, riktlinjer och exempel, finns på följande sidor:
* [Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md)
* [Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps-projekt](how-to-devops-project.md)
* [Azure IoT Edge Jenkins-plugin-programmet](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub-lagringsplatsen](https://github.com/toolboc/IoTEdge-DevOps)
