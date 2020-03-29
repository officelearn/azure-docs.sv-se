---
title: Utvecklingsmiljö för Azure IoT Edge | Microsoft-dokument
description: Lär dig mer om system som stöds och utvecklingsverktyg från första part som hjälper dig att skapa IoT Edge-moduler
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96078be20e8048e481a994fefc169e48ab1d8459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511101"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Förbered din utvecklings- och testmiljö för IoT Edge

Azure IoT Edge flyttar din befintliga affärslogik till enheter som arbetar på gränsen. Om du vill förbereda dina program och arbetsbelastningar så att de körs som [IoT Edge-moduler](iot-edge-modules.md)måste du skapa dem som behållare. Den här artikeln innehåller vägledning om hur du konfigurerar utvecklingsmiljön så att du kan skapa en IoT Edge-lösning. När du har konfigurerat din utvecklingsmiljö kan du lära dig att [utveckla dina egna IoT Edge-moduler](module-development.md).

I alla IoT Edge-lösningar finns det minst två maskiner att tänka på. En är IoT Edge-enheten (eller enheterna) själv, som kör IoT Edge-modulen. Den andra är den utvecklingsdator som du använder för att skapa, testa och distribuera moduler. Denna artikel fokuserar främst på utvecklingsmaskinen. För teständamål kan de två maskinerna vara desamma. Du kan köra IoT Edge på utvecklingsdatorn och distribuera moduler till den.

## <a name="operating-system"></a>Operativsystem

Azure IoT Edge körs på en specifik uppsättning [operativsystem som stöds](support.md). För utveckling för IoT Edge kan du använda de flesta operativsystem som kan köra en behållarmotor. Behållarmotorn är ett krav på utvecklingsmaskinen att bygga dina moduler som behållare och skicka dem till ett behållarregister.

Om din utvecklingsdatur inte kan köra Azure IoT Edge fortsätter du i den här artikeln om hur du kan testa [testverktyg](#testing-tools) som hjälper dig att testa och felsöka lokalt.

Operativsystemet på utvecklingsmaskinen behöver inte matcha operativsystemet på din IoT Edge-enhet. Behållaroperativsystemet måste dock vara konsekvent mellan utvecklingsmaskinen och IoT Edge-enheten. Du kan till exempel utveckla moduler på en Windows-dator och distribuera dem till en Linux-enhet. Windows-datorn måste köra Linux-behållare för att bygga modulerna för Linux-enheten.

## <a name="container-engine"></a>Containermotor

Det centrala konceptet med IoT Edge är att du kan fjärrstyra din affärs- och molnlogik till enheter genom att paketera den i behållare. För att bygga behållare behöver du en behållarmotor på din utvecklingsmaskin.

Den enda containermotor som stöds för IoT Edge-enheter i produktion är Moby. Alla behållarmotorer som är kompatibla med Open Container Initiative, som Docker, kan dock skapa IoT Edge-modulavbildningar.

## <a name="development-tools"></a>Utvecklingsverktyg

Både Visual Studio och Visual Studio Code har tillägg för att utveckla IoT Edge-lösningar. Dessa tillägg innehåller språkspecifika mallar som hjälper dig att skapa och distribuera nya IoT Edge-scenarier. Azure IoT Edge-tilläggen för Visual Studio och Visual Studio-kod hjälper dig att koda, skapa, distribuera och felsöka dina IoT Edge-lösningar. Du kan skapa en hel IoT Edge-lösning som innehåller flera moduler och tilläggen uppdaterar automatiskt en distributionsmanifestmall med varje nytt modultillägg. Med tilläggen kan du även hantera dina IoT-enheter inifrån Visual Studio eller Visual Studio Code. Distribuera moduler till en enhet, övervaka status och visa meddelanden när de anländer till IoT Hub. Båda tilläggen använder [IoT EdgeHub-utvecklingsverktyget](#iot-edgehub-dev-tool) för att aktivera lokal körning och felsökning av moduler på utvecklingsmaskinen.

Om du föredrar att utveckla med andra redigerare eller från CLI, azure IoT Edge dev-verktyget ger kommandon så att du kan utveckla och testa från kommandoraden. Du kan skapa nya IoT Edge-scenarier, skapa modulavbildningar, köra moduler i en simulator och övervaka meddelanden som skickas till IoT Hub.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-tillägg

Azure IoT Edge-tillägget för Visual Studio-kod tillhandahåller IoT Edge-modulmallar som bygger på programmeringsspråk, inklusive C, C#, Java, Node.js och Python samt Azure-funktioner i C#.

Mer information och hämtning finns i [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Förutom IoT Edge-tilläggen kan det vara bra att installera ytterligare tillägg för utveckling. Du kan till exempel använda [Docker-stöd för Visual Studio-kod för](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) att hantera bilder, behållare och register. Dessutom har alla de större språk som stöds tillägg för Visual Studio-kod som kan hjälpa när du utvecklar moduler.

#### <a name="prerequisites"></a>Krav

Modulmallarna för vissa språk och tjänster har förutsättningar som krävs för att skapa projektmapparna på utvecklingsmaskinen med Visual Studio Code.

| Modulmall | Krav |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Utveckling Kit 10](https://aka.ms/azure-jdks) <li> [Ange variabeln JAVA_HOME miljö](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modul generator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019 förlängning

Azure IoT Edge-verktygen för Visual Studio tillhandahåller en IoT Edge-modulmall som är byggd på C# och C.

Mer information och hämtning finns i [Azure IoT Edge Tools för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) eller [Azure IoT Edge Tools för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge-utvecklingsverktyget

Utvecklingsverktyget för Azure IoT Edge förenklar IoT Edge-utvecklingen med kommandoradsförmågor. Det här verktyget tillhandahåller CLI-kommandon för att utveckla, felsöka och testa moduler. IoT Edge-utvecklingsverktyget fungerar med utvecklingssystemet, oavsett om du har installerat beroendena på din dator manuellt eller använder IoT Edge-utvecklingsbehållaren.

Mer information och kom igång finns i [IoT Edge dev tool wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testverktyg

Det finns flera testverktyg som hjälper dig att simulera IoT Edge-enheter eller felsökningsmoduler mer effektivt. I följande tabell visas en jämförelse på hög nivå mellan verktygen och sedan beskrivs varje verktyg mer specifikt i enskilda avsnitt.

Endast IoT Edge-körningen stöds för produktionsdistributioner, men med följande verktyg kan du simulera eller enkelt skapa IoT Edge-enheter för utvecklings- och testningsändamål. Dessa verktyg utesluter inte varandra, men kan arbeta tillsammans för en komplett utvecklingsupplevelse.

| Verktyg | Även känd som | Plattformar som stöds | Bäst för |
| ---- | ------------- | ------------------- | --------- |
| Utvecklingsverktyget för IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Simulerar en enhet för felsökning av moduler. |
| IoT Edge-utvecklingsbehållare | microsoft/iotedgedev | Windows, Linux, MacOS | Utveckla utan att installera beroenden. |
| IoT Edge-körning i en behållare | iotedgec (iotedgec) | Windows, Linux, MacOS, ARM | Testning på en enhet som kanske inte stöder körningen. |
| Behållare för IoT Edge-enhet | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testa ett scenario med många IoT Edge-enheter i stor skala. |

### <a name="iot-edgehub-dev-tool"></a>Utvecklingsverktyget för IoT EdgeHub

Utvecklingsverktyget för Azure IoT EdgeHub ger en lokal utvecklings- och felsökningsupplevelse. Verktyget hjälper till att starta IoT Edge-moduler utan IoT Edge-körningen så att du kan skapa, utveckla, testa, köra och felsöka IoT Edge-moduler och lösningar lokalt. Du behöver inte skicka avbildningar till ett behållarregister och distribuera dem till en enhet för testning.

IoT EdgeHub-utvecklingsverktyget har utformats för att fungera tillsammans med tilläggen för Visual Studio- och Visual Studio-kod, samt med IoT Edge-utvecklingsverktyget. Den stöder inre loop utveckling samt yttre loop testning, så integrerar med DevOps verktyg också.

Mer information och installation finns i [Azure IoT EdgeHub-utvecklingsverktyg](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge-utvecklingsbehållare

Azure IoT Edge dev-behållaren är en Docker-behållare som har alla beroenden som du behöver för IoT Edge-utveckling. Den här behållaren gör det enkelt att komma igång med vilket språk du vill utveckla i, inklusive C#, Python, Node.js och Java. Allt du behöver installera är en containermotor, som Docker eller Moby, för att dra behållaren till din utvecklingsmaskin.

Mer information finns i [Azure IoT Edge dev-behållaren](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge-körning i en behållare

IoT Edge-körningen i en behållare ger en fullständig körning som tar enhetens anslutningssträng som en miljövariabel. Med den här behållaren kan du testa IoT Edge-moduler och scenarier på ett system som kanske inte stöder körningen internt, till exempel MacOS. Alla moduler som du distribuerar startas utanför körningsbehållaren. Om du vill att körningen och eventuella distribuerade moduler ska finnas i samma behållare bör du tänka på IoT Edge-enhetsbehållaren i stället.

Mer information finns i [Köra Azure IoT Edge i en behållare](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Behållare för IoT Edge-enhet

IoT Edge-enhetsbehållaren är en komplett IoT Edge-enhet, redo att startas på alla maskiner med en behållarmotor. Enhetsbehållaren innehåller IoT Edge-körningen och en behållarmotor själv. Varje instans av behållaren är en fullt fungerande självetablering IoT Edge-enhet. Enhetsbehållaren stöder fjärrfelsökning av moduler, så länge det finns en nätverksväg till modulen. Enhetsbehållaren är bra för att snabbt skapa ett stort antal IoT Edge-enheter för att testa storskaliga scenarier eller Azure Pipelines. Det stöder också distribution till kubernetes via rodret.

Mer information finns i [Azure IoT Edge-enhetsbehållare](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-verktyg

När du är redo att utveckla storskaliga lösningar för omfattande produktionsscenarier kan du dra nytta av moderna DevOps-principer, inklusive automatisering, övervakning och strömlinjeformade programvarutekniska processer. IoT Edge har tillägg för att stödja DevOps-verktyg, inklusive Azure DevOps, Azure DevOps-projekt och Jenkins. Om du vill anpassa en befintlig pipeline eller använda ett annat DevOps-verktyg som CircleCI eller TravisCI kan du göra det med CLI-funktionerna som ingår i IoT Edge-utvecklingsverktyget.

Mer information, vägledning och exempel finns på följande sidor:

* [Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md)
* [Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps-projekt](how-to-devops-project.md)
* [Azure IoT Edge Jenkins plugin](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub repo](https://github.com/toolboc/IoTEdge-DevOps)
