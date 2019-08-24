---
title: Ansluta en IoT Plug and Play-enhet till IoT Central | Microsoft Docs
description: Som enhets utvecklare kan du läsa om hur du använder Visual Studio Code för att skapa och testa en IoT Plug and Play-enhet som ansluter till IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997231"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Använd Visual Studio Code för att skapa en IoT Plug and Play-enhet som ansluter till IoT Central

Den här instruktions guiden visar hur du:

* Som operatör kan du lägga till och konfigurera en riktig enhet i ditt Azure IoT Central-program.

* Som enhets utvecklare kan du använda Visual Studio Code för att skapa en [IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md) -enhet som ansluter till ditt IoT Central-program.

Du använder en [enhets kapacitets modell](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) för att definiera enheten som ansluter till IoT Central. I den här guiden använder du en modell som definierar en miljö sensor enhet.

Enhets utvecklaren använder modellen för att generera enhets klient kod och verktyget använder modellen för att [skapa en enhets mall](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i IoT Central. Modellen fungerar som ett avtal mellan din enhet och ditt IoT Central program.

I avsnittet i den här guiden som beskriver hur du skapar den genererade koden förutsätter vi att du använder Windows.

I den här guiden får du lära du dig att:

* Importera en enhets kapacitets modell till en enhets mall i IoT Central
* Lägg till en instrument panel till mallen som visar telemetri för enheter
* Lägg till en riktig enhet från mallen
* Importera en enhets kapacitets modell till Visual Studio Code
* Skapa ett klient program för C-enheter från modellen
* Bygg enhets klient programmet för C och Anslut det till IoT Central

## <a name="prerequisites"></a>Förutsättningar

Om du vill testa enhets koden i den här guiden måste du ha ett IoT Central-program som skapats från mallen för för **hands versions** program. Om du inte redan har ett IoT Central program att använda slutför du snabb starten [skapa ett Azure IoT Central-program (för hands versions funktioner)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

Om du vill arbeta med enhets kapacitets modellen i den här hand boken behöver du:

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio Code är tillgängligt för flera plattformar
* Azure IoT Device Workbench-tillägg för Visual Studio Code. Använd följande steg för att installera tillägget Azure IoT Device Workbench i VS Code:

    1. I VS Code väljer du fliken **tillägg** .
    1. Sök efter **Azure IoT Device Workbench**.
    1. Välj **Installera**.

    > [!NOTE]
    > Den aktuella versionen av kod generatorn i tillägget har inte stöd för **punkt** -och **vektor** schema typer, semantiska typer av **acceleration**, **hastighet**och **plats** . Dessa scheman och semantiska typer stöds av IoT Central.

    > [!NOTE]
    > För att kunna arbeta med IoT Central måste enhets kapacitets modellen ha alla gränssnitt som definierats infogade i samma fil.

För att skapa den genererade C-koden i Windows i den här hand boken behöver du:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **NuGet Package Manager** -komponenten och **Skriv bords utveckling med C++**  arbets belastning när du installerar Visual Studio.
* [Git](https://git-scm.com/download)
* [Cmake](https://cmake.org/download/) – när du installerar **cmake**väljer du alternativet **Lägg till cmake i System Sök vägen**.
* En lokal kopia av Azure IoT C SDK:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Om du vill följa anvisningarna i enheten i slutet av den här instruktionen måste du också installera:

* [Node.js](https://nodejs.org)
* [Verktyget DPS-keygen](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Skapa enhets mall

Skapa en mapp med namnet **pnp_app** i mappen **Azure-IoT-SDK-c** som innehåller Azure IoT c SDK som du har klonat. Ladda ned [EnvironmentalSensor. capabilitymodel. JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) -enhetens kapacitets modell från GitHub och spara filen i **pnp_app** -mappen. Ersätt de båda instanserna `<YOUR_COMPANY_NAME_HERE>` av med ditt namn. Använd bara tecknen a-z, A-Z, 0-9 och under streck. Anteckna det fullständiga värdet för `"@id"` fältet som unikt identifierar enhetens kapacitets modell. du behöver den senare. Den här modellen innehåller två gränssnitt:

* Det vanliga **DeviceManagement** -gränssnittet.
* det anpassade **EnvironmentalSensor** -gränssnittet.

Så här skapar du en enhets mal len för miljö sensor enheten i IoT Central:

1. Gå till **Device templates** -sidan och välj **+ ny**. Välj sedan **anpassad**.

1. Ange **miljö sensor** som namn på enhets mal len.

1. Välj **import kapacitets modell**. Leta sedan reda på den **EnvironmentalSensor. capabilitymodel. JSON** som du skapade och välj **Öppna**. De två gränssnitten, **enhets informationen** och **miljö sensorn**visas i **modellen för miljö sensor kapacitet**.

1. Välj **vyer** och **visualisera sedan enheten**.

1. I listan över fält som du kan lägga till i instrument panelen väljer du de två **telemetridata** , **fuktighet** och **temperatur** och väljer **kombinera**. Välja sedan **Spara**.

Nu har du en enhets mal len som använder **miljö sensor** modellen och som har en enkel instrument panel för att Visa telemetri från en enhet.

## <a name="publish-the-template-and-add-a-real-device"></a>Publicera mallen och Lägg till en riktig enhet

Om du vill publicera mallen och lägga till en riktig enhet går du till sidan **mallar för enheter** och väljer enhets mal len för **miljö sensor** . Välj **publicera**, granska informationen och välj **publicera**.

Innan du ansluter ett klient program måste du lägga till en enhet på sidan **enheter** och hämta anslutnings informationen:

1. Gå till sidan **enheter** och välj **miljö sensor**. På sidan **enheter** kan du hantera enheter som kan ansluta till ditt program.

1. Om du vill lägga till en riktig enhet väljer du **+ ny**, ändrar enhets-ID till **Sensor01**och väljer **skapa**. Se till att den simulerade är **avstängd**.

1. I listan med enheter väljer du din **miljö sensor** enhet. Välj sedan **Anslut**.

1. Anteckna omfångs **-ID**, **enhets-ID**och **primär nyckel**. Välj sedan **Stäng**.

## <a name="generate-a-device-client-application"></a>Generera ett enhets klient program

Du kan använda Visual Studio Code för att generera ett Skeleton enhets klient program från enhetens kapacitets modell:

1. Starta Visual Studio Code och öppna mappen **pnp_app** .

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj sedan **generera enhets kod stub**.

1. Välj modell filen **EnvironmentalSensor. capabilitymodel. JSON** Device capability.

1. Ange **sensor_app** som projekt namn.

1. Välj **ANSI C** som språk.

1. Välj **cmake-projekt** som mål.

1. Välj **via DPS (Device Provisioning service) symmetrisk nyckel** som anslutnings metod.

Visual Studio Code öppnar ett nytt fönster med den genererade C-koden i mappen **sensor_app** .

## <a name="add-connection-details-to-the-device-client"></a>Lägg till anslutnings information till enhets klienten

Om du vill lägga till anslutnings informationen till din enhets klient öppnar du **main. c** i den mapp som innehåller den genererade koden:

1. Ersätt `[DPS Id Scope]` med det **omfattnings-ID-** värde som du antecknade tidigare.

1. Ersätt `[DPS symmetric key]` med värdet för **primär nyckel** som du antecknade tidigare.

1. Ersätt `[device registration Id]` med värdet för **enhets-ID** som du antecknade tidigare.

1. Spara ändringarna.

## <a name="build-and-run-the-client"></a>Skapa och kör-klienten

Om du vill skapa och köra-klienten måste du anpassa versionen för Azure IoT C SDK:

1. Öppna filen **CMakeLists. txt** i roten i mappen **Azure-IoT-SDK-c** .

1. Lägg till följande rad i slutet av den här filen för att inkludera den nya klient appen i versionen:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Spara ändringarna.

1. Öppna en kommando tolk och gå till mappen **Azure-IoT-SDK-c** .

1. Kör följande kommandon för att skapa programmet:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Kör programmet genom att köra följande kommando från samma kommando tolk:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>Visa Telemetrin

Efter några minuter kan du se Telemetrin från enheten på enhets instrument panelen i ditt IoT Central-program.

## <a name="connect-device-first"></a>Anslut enheten – första

Du kan ansluta en IoT Plug and Play-enhet via en enhets-första anslutning enligt beskrivningen i [anslutnings barhet](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Identifierings processen följer den här ordningen:

1. Associeras med enhets mal len om den redan har publicerats i IoT Central-programmet.

1. Hämtar kapacitets modellen från den [offentliga lagrings platsen](https://aka.ms/ACFI) för publicerade och certifierade kapacitets modeller.

Med hjälp av Visual Studio Code och det **genererade enhets kod stub** -kommandot som refereras ovan, kan du följa dessa steg för att ansluta enhets-först och automatiskt placera den publicerade enhetens kapacitets modell från den offentliga lagrings platsen i IoT Central:

1. Använd en befintlig enhets kapacitets modell som har publicerats i den offentliga lagrings platsen. Du behöver fullständig modell för enhets kapacitet och anteckna den här modellens URN.

1. Följ stegen ovan för att [generera ett enhets klient program](#generate-a-device-client-application) för att använda Visual Studio Code och generera enhets koden.

1. Från IoT Central-programmet går du till fliken **Administration** och markerar avsnittet **enhets anslutning** . Anteckna **omfattnings-ID** och **primär nyckel** värden för ditt program.

    > [!NOTE]
    > Den **primära nyckeln** som visas på den här sidan är en signatur för delad åtkomst för en grupp som du kan använda för att generera flera enhets nycklar för ditt program.

1. Använd verktyget [DPS keygen](https://www.npmjs.com/package/dps-keygen) för att generera en enhets nyckel från den primära nyckeln som du antecknade tidigare. Skapa enhets nyckeln genom att köra följande kommando:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Följ stegen i föregående avsnitt, [Lägg till anslutnings information i enhets klienten](#add-connection-details-to-the-device-client) för att lägga **till scope-ID**, **primär nyckel**och **enhets-ID**.

1. Följ stegen i föregående avsnitt för att [skapa och köra-klienten](#build-and-run-the-client).

1. Nu kan du se att enheten är ansluten till ditt IoT Central-program och automatiskt placera enhets kapacitets modellen från den offentliga lagrings platsen som en enhets mall.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en riktig enhet till IoT Central, är ett föreslaget nästa steg att lära dig mer om enhets mallar i [Konfigurera en enhets mall](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
