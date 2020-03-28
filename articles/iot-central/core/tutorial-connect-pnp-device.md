---
title: Självstudiekurs – Anslut en IoT Plug and Play-enhet (förhandsversion) till Azure IoT Central
description: Den här självstudien visar hur du använder en enhetskapacitetsmodell för att generera enhetskod. Kör sedan enhetskoden, se enheten ansluta till ditt IoT Central-program och använd de autogenererade vyerna.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80064422"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Självstudiekurs: Använd en enhetskapacitetsmodell för att skapa en IoT Plug and Play-enhet (förhandsgranskning) och ansluta den till ditt IoT Central-program

En _DCM-modell (Device Capability Model)_ beskriver funktionerna i en [IoT Plug and Play-enhet (preview).](../../iot-pnp/overview-iot-plug-and-play.md) IoT Central kan använda en DCM för att skapa en enhetsmall och visualiseringar för en enhet när enheten ansluts för första gången.

Stöd för [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) är i förhandsversion och stöds endast i valda områden.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Använd Visual Studio-kod för att skapa en IoT Plug and Play-enhet (förhandsgranskning) med hjälp av en DCM.
> * Kör enhetskoden i Windows och se den ansluta till ditt IoT Central-program.
> * Visa den simulerade telemetri som enheten skickar.

## <a name="prerequisites"></a>Krav

Slutför snabbstarten [Skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central-program med hjälp av **den anpassade appen > anpassad programmall.**

För att slutföra den här självstudien måste du installera följande programvara på din lokala dator:

* [Bygg verktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med **C++-byggverktyg** och **Nuget-pakethanterares** komponentarbetsbelastningar. Eller om du redan har [Visual Studio (Community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbetsbelastningar installerade.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) - när du installerar **CMake**väljer du alternativet **Lägg till CMake i systemet PATH**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Verktyget: `dps-keygen`

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Gör så här för att installera tilläggspaketet för Azure IoT Tools i VS-kod:

1. Välj fliken **Tillägg** i VS-kod.
1. Sök efter **Azure IoT Tools**.
1. Välj **Installera**.

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här självstudien använder du [Vcpkg-bibliotekshanteraren](https://github.com/microsoft/vcpkg) för att installera Azure IoT C-enheten SDK i utvecklingsmiljön.

1. Öppna en kommandotolk. Kör följande kommando för att installera Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Sedan, för att ansluta användaromfattande [integration](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md), kör följande kommando. Första gången du kör det här kommandot krävs administrativa rättigheter:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installera Azure IoT C-enhet SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generera enhetsnyckel

Om du vill ansluta en enhet till ett IoT Central-program behöver du en enhetsnyckel. Så här skapar du en enhetsnyckel:

1. Logga in på IoT Central-programmet som du skapade med hjälp av den **anpassade programmallen** i snabbstarten [Skapa ett Azure IoT Central-program.](./quick-deploy-iot-central.md)

1. Gå till sidan **Administration** och välj **Enhetsanslutning**.

1. Anteckna **ID-scopet** och **primärnyckeln** som visas när du väljer **Visa nycklar**. Du använder dessa värden senare i den här självstudien.

    ![Enhetsanslutning](./media/tutorial-connect-pnp-device/device-connection.png)

1. Öppna en kommandotolk och kör följande kommando för att generera en enhetsnyckel:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Anteckna den genererade _enhetsnyckeln_och använd det här värdet i ett senare steg i den här självstudien.

## <a name="download-your-model"></a>Ladda ner din modell

I den här självstudien använder du den offentliga DCM för en MxChip IoT DevKit-enhet. Du behöver inte en faktisk DevKit-enhet för att köra koden, i den här självstudien kompilerar du koden som ska köras på Windows.

1. Skapa en `central_app` mapp som heter och öppna den i VS-kod.

1. Använd **Ctrl+Skift+P** för att öppna kommandopaletten, ange **IoT Plug and Play**och välj Öppna **modelldatabas .** Välj **Offentlig databas**. VS-kod visar en lista över domänkontrollanter i den offentliga modelldatabasen.

1. Välj **MXChip IoT DevKit** DCM `urn:mxchip:mxchip_iot_devkit:1`med ID . Välj sedan **Hämta**. Du har nu en kopia av `central_app` DCM i mappen.

![Modellarkiv och DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> För att fungera med IoT Central måste enhetskapacitetsmodellen ha alla gränssnitt definierade infogade i samma fil.

## <a name="generate-the-c-code-stub"></a>Generera C-kod stub

Nu har du **MXChip IoT DevKit** DCM och dess tillhörande gränssnitt, kan du generera enhetskoden som implementerar modellen. Så här genererar du C-kodssubsen i VS-kod:

1. När mappen med DCM-filer är öppna använder du **Ctrl+Skift+P** för att öppna kommandopaletten, anger **IoT Plug and Play**och väljer **Generera Enhetskodsstub.**

    > [!NOTE]
    > Första gången du använder IoT Plug and Play Code Generator verktyget, det tar några sekunder att ladda ner.

1. Välj **filen MXChip IoT DevKit** DCM som du just har hämtat.

1. Ange projektnamnet **devkit_device**.

1. Välj **ANSI C** som språk.

1. Välj **symmetrisk nyckel via DPS (Device Provisioning Service)** som anslutningsmetod.

1. Välj **CMake Project i Windows** som projekttyp. Välj inte **MXChip IoT DevKit Project**, det här alternativet är för när du har en riktig DevKit-enhet.

1. Välj **Via Vcpkg** som sätt att inkludera SDK.

1. VS-kod öppnar ett nytt fönster med `devkit_device` genererade enhetskodssub-filer i mappen.

![Genererad enhetskod](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Skapa koden

Du använder enheten SDK för att skapa den genererade enhetskoden stub. Programmet du bygger simulerar en **MXChip IoT DevKit-enhet** och ansluter till ditt IoT Central-program. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I en kommandotolk `cmake` skapar du `devkit_device` en underkatalog i mappen och navigerar till den mappen:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade koden stub. Ersätt `<directory of your Vcpkg repo>` platshållaren med sökvägen till kopian av **Vcpkg-databasen:**

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de byggverktyg du använder:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. När versionen har slutförts körs programmet i samma kommandotolk. Ersätt `<scopeid>` `<devicekey>` och med de värden som du noterade tidigare:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Enhetsprogrammet börjar skicka data till IoT Hub. Ibland ser du `Error registering device for DPS` felet första gången du kör föregående kommando. Om det här felet visas försöker du igen kommandot.

## <a name="view-the-device"></a>Visa enheten

När enhetskoden har anslutits till IoT Central kan du visa de egenskaper och den telemetri som skickas:

1. I ditt IoT Central-program går du till sidan **Enheter** och väljer **mxchip-01-enheten.** Den här enheten lades automatiskt till när enhetskoden kopplades:

    ![Översiktssida](./media/tutorial-connect-pnp-device/overview-page.png)

    Efter ett par minuter visar den här sidan diagram över den telemetri som enheten skickar.

1. Välj sidan **Om om** du vill visa egenskapsvärdena som enheten har skickat.

1. Välj den sida **kommandon** som ska anropas på enheten. Du kan se enheten svara vid kommandotolken som kör enhetskoden.

1. Gå till sidan **Enhetsmallar** om du vill se mallen som IoT Central skapade från DCM i den offentliga databasen:

    ![Sidan Enhetsmallar](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du ansluter en IoT Plug and Play-enhet (preview) som genererades från en DCM i den offentliga modelldatabasen.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till vägledningen:

> [!div class="nextstepaction"]
> [Definiera en ny IoT-enhetstyp](./howto-set-up-template.md)
