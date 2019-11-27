---
title: Ansluta en IoT Plug and Play för hands version till Azure IoT Central | Microsoft Docs
description: Använd en enhets kapacitets modell för att generera enhets kod. Kör sedan enhets koden, se enheten Anslut till ditt IoT Central program och Använd de automatiskt genererade vyerna.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: be03684f89382f198c13540bbdfb3de5bf8513a6
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404556"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Självstudie: Använd en enhets kapacitets modell för att skapa en IoT Plug and Play-enhet och ansluta den till ditt IoT Central-program

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

En _enhets kapacitets modell_ (DCM) beskriver funktionerna i en [IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) -enhet. IoT Central kan använda ett DCM för att skapa en enhets mall och visualiseringar för en enhet när enheten ansluter för första gången.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd Visual Studio Code för att skapa en IoT Plug and Play-enhet med hjälp av ett DCM.
> * Kör enhets koden i Windows och se att den är ansluten till ditt IoT Central-program.
> * Visa den simulerade telemetri som enheten skickar.

## <a name="prerequisites"></a>Förutsättningar

Slutför snabb starten [skapa ett Azure IoT Central-program (för hands versions funktioner)](./quick-deploy-iot-central.md) för att skapa ett IoT Central program med hjälp av den **anpassade appen > för hands versions program** mal len

För att slutföra den här självstudien måste du installera följande program vara på den lokala datorn:

* [Bygg verktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med  **C++ build-verktyg** och **NuGet Package Manager-komponentens** arbets belastningar. Eller om du redan har [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbets belastningar installerade.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/) – när du installerar **cmake**väljer du alternativet **Lägg till cmake i System Sök vägen**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* `dps-keygen`s verktyget:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Använd följande steg för att installera tilläggs paketet för Azure IoT-verktyg i VS Code:

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter **Azure IoT-verktyg**.
1. Välj **Installera**.

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här självstudien använder du [Vcpkg](https://github.com/microsoft/vcpkg) Library Manager för att installera Azure IoT C-enhetens SDK i utvecklings miljön.

1. Öppna en kommandotolk. Kör följande kommando för att installera Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Kör sedan följande kommando för att koppla samman [integreringen](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)med hela användaren. Första gången du kör det här kommandot krävs administrativa rättigheter:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installera Azure IoT C-Vcpkg för enhets-SDK:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generera enhets nyckel

Om du vill ansluta en enhet till ett IoT Central-program behöver du en enhets nyckel. Så här skapar du en enhets nyckel:

1. Logga in på IoT Central programmet som du skapade med hjälp av den **anpassade appen > för hands version av appen** [skapa ett Azure IoT Central-program (förhands gransknings funktioner)](./quick-deploy-iot-central.md) snabb start.

1. Gå till **administrations** sidan och välj **enhets anslutning**.

1. Anteckna **ID-omfånget** och den **primära nyckeln** som visas när du väljer **Visa nycklar**. Du använder dessa värden senare i den här självstudien.

    ![Enhets anslutning](./media/tutorial-connect-pnp-device/device-connection.png)

1. Öppna en kommando tolk och kör följande kommando för att skapa en enhets nyckel:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Anteckna den genererade _enhets nyckeln_, du använder det här värdet i ett senare steg i den här självstudien.

## <a name="download-your-model"></a>Ladda ned din modell

I den här självstudien använder du offentliga DCM för en MxChip IoT DevKit-enhet. Du behöver ingen faktisk DevKit-enhet för att köra koden. i den här självstudien kompilerar du koden som ska köras i Windows.

1. Skapa en mapp med namnet `central_app` och öppna den i VS Code.

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten, ange **IoT plug and Play**och välj **Öppna modell lagrings plats**. Välj **offentlig lagrings plats**. VS Code visar en lista över DCMs i den offentliga modellens lagrings plats.

1. Välj **MXChip IoT DevKit** DCM med ID `urn:mxchip:mxchip_iot_devkit:1`. Välj sedan **Ladda ned**. Nu har du en kopia av DCM i mappen `central_app`.

![Modell lagring och DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> För att kunna arbeta med IoT Central måste enhets kapacitets modellen ha alla gränssnitt som definierats infogade i samma fil.

## <a name="generate-the-c-code-stub"></a>Generera C-koden stub

Nu har du **MXChip IoT-DevKit** DCM och tillhör ande gränssnitt, kan du generera enhets koden som implementerar modellen. Så här genererar du C-koden stub i VS Code:

1. När mappen med DCM-filer är öppen använder du **Ctrl + Shift + P** för att öppna paletten kommando, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

    > [!NOTE]
    > Första gången du använder IoT Plug and Play Code Generator-verktyget tar det några sekunder att ladda ned.

1. Välj den **MXChip IoT DevKit** DCM-fil som du precis har laddat ned.

1. Ange projekt namnet **devkit_device**.

1. Välj **ANSI C** som språk.

1. Välj **via DPS (Device Provisioning service) symmetrisk nyckel** som anslutnings metod.

1. Välj **cmake-projekt i Windows** som projekt typ. Välj inte **MXChip IoT DevKit Project**, det här alternativet är för när du har en riktig DevKit-enhet.

1. Välj **via Vcpkg** som metod för att inkludera SDK: n.

1. VS Code öppnar ett nytt fönster med genererad enhets kod stub-filer i mappen `devkit_device`.

![Genererad enhets kod](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Skapa koden

Du kan använda enhets-SDK: n för att skapa den genererade enhets koden stub. Det program som du skapar simulerar en **MXChip IoT DevKit** -enhet och ansluter till ditt IoT Central-program. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I kommando tolken skapar du en `cmake` under katalog i mappen `devkit_device` och navigerar till mappen:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade koden stub. Ersätt `<directory of your Vcpkg repo>` plats hållaren med sökvägen till din kopia av **Vcpkg** -lagringsplatsen:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de build-verktyg som du använder:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. När versionen har slutförts kör du ditt program i samma kommando tolk. Ersätt `<scopeid>` och `<devicekey>` med de värden som du antecknade tidigare:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Enhets programmet börjar skicka data till IoT Hub. Ibland visas fel `Error registering device for DPS` första gången du kör föregående kommando. Om du ser det här felet kan du försöka köra kommandot igen.

## <a name="view-the-device"></a>Visa enheten

När din enhets kod ansluter till din IoT Central kan du Visa de egenskaper och telemetri som skickas:

1. I ditt IoT Central-program går du till sidan **enheter** och väljer **mxchip-01-** enheten. Enheten lades automatiskt till när enhets koden anslöt:

    ![Översikts sida](./media/tutorial-connect-pnp-device/overview-page.png)

    Efter ett par minuter visar den här sidan diagram över telemetri som enheten skickar.

1. Välj sidan **om** för att se de egenskaps värden som enheten skickade.

1. Välj **kommando** sidan för att anropa kommandon på enheten. Du kan se att enheten svarar vid kommando tolken som kör enhets koden.

1. Gå till sidan **enhetsspecifika mallar** om du vill se mallen som IoT Central skapade från DCM i den offentliga lagrings platsen:

    ![Sidan enhets mallar](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter en IoT Plug and Play-enhet som genererades från ett DCM i den offentliga modellens lagrings plats.

Om du vill veta mer om DCMs och hur du skapar egna modeller kan du fortsätta till instruktions guiden:

> [!div class="nextstepaction"]
> [Skapa en enhets grupp](./tutorial-use-device-groups.md)
