---
title: Bygg en IoT-Plug and Play förhands gransknings enhet som är klar för certifiering | Microsoft Docs
description: Som enhets utvecklare får du lära dig hur du kan bygga en IoT-Plug and Play för hands version som är klar för certifiering.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ce7d3ee8a0d05d837bc0049cba688cffe14d8a8c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721650"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Bygg en IoT Plug and Play förhands gransknings enhet som är klar för certifiering

I den här självstudien beskrivs hur, som enhets utvecklare, kan du bygga en IoT-Plug and Play för hands version som är redo för certifiering.

Certifierings testerna kontrollerar att:

- Din IoT Plug and Play enhets kod installeras på enheten.
- Din IoT Plug and Play enhets kod har skapats med Azure IoT SDK.
- Enhets koden stöder [Azure-IoT Hub Device Provisioning service](../iot-dps/about-iot-dps.md).
- Enhets koden implementerar enhets informations gränssnittet.
- Kapacitets modellen och enhets koden fungerar med IoT Central.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure IoT Tools för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack

Du måste också slutföra en enhets [kapacitets modell för att skapa en enhets](quickstart-create-pnp-device-windows.md) snabb start för Windows. Snabb starten visar hur du konfigurerar din utvecklings miljö med Vcpkg och skapar ett exempel projekt.

## <a name="store-a-capability-model-and-interfaces"></a>Lagra en kapacitets modell och gränssnitt

För IoT Plug and Play-enheter måste du skapa en kapacitets modell och gränssnitt som definierar enhetens funktioner som JSON-filer.

Du kan lagra dessa JSON-filer på tre olika platser:

- Den offentliga modellens lagrings plats.
- Företagets modell databas.
- På din enhet.

För närvarande måste filerna lagras antingen i företagets modell lager eller i den offentliga modell databasen för att certifiera enheten.

## <a name="include-the-required-interfaces"></a>Ta med de gränssnitt som krävs

Om du vill skicka certifierings processen måste du inkludera och implementera gränssnittet för **enhets information** i din kapacitets modell. Det här gränssnittet har följande identifiering:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Om du har slutfört [snabb starten: använda en enhets kapacitets modell för att skapa en enhet](quickstart-create-pnp-device-windows.md)har du redan inkluderat **enhets informations** gränssnittet i din modell.

Om du vill inkludera gränssnittet för **enhets information** i din enhets modell lägger du till gränssnitts-ID: t i egenskapen `implements` för kapacitets modellen:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Visa **enhets informations** gränssnittet i vs Code:

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj **IoT plug and Play öppna modellens databas** kommando. Välj **Öppna offentlig modell databas**. Den offentliga modellens lagrings plats öppnas i VS Code.

1. I lagrings platsen för den offentliga modellen väljer du fliken **gränssnitt** , väljer filter ikonen och anger **enhets information** i fältet filter.

1. Om du vill skapa en lokal kopia av **enhets informations** gränssnittet väljer du det i den filtrerade listan och väljer sedan **Ladda ned**. VS Code visar gränssnitts filen.

Visa **enhets informations** gränssnittet med hjälp av Azure CLI:

1. [Installera Azure IoT CLI-tillägget](howto-install-pnp-cli.md).

1. Använd följande Azure CLI-kommando för att visa ett gränssnitt med enhets informationens gränssnitts-ID:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Mer information finns i [Installera och använda Azure IoT-tillägget för Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Uppdatera enhets kod

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Aktivera enhets etablering via Azure IoT Device Provisioning-tjänsten (DPS)

För att certifiera enheten måste den Aktivera etablering via [Azure IoT Device Provisioning-tjänsten (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Om du vill lägga till möjligheten att använda DPS kan du generera C-koden stub i VS Code. Följ de här stegen:

1. Öppna mappen med DCM-filen i VS Code, Använd **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och välj **generera enhets kod stub**.

1. Välj den DCM-fil som du vill använda för att generera enhets kodens stub.

1. Ange projekt namnet, t. ex. **sample_device**. Detta är namnet på enhets programmet.

1. Välj **ANSI C** som språk.

1. Välj **via DPS (Device Provisioning service) symmetrisk nyckel** som anslutnings metod.

1. Välj **cmake-projekt i Windows** som projekt mal len.

1. Välj **via Vcpkg** som metod för att inkludera enhets-SDK: n.

1. VS Code öppnar ett nytt fönster med genererad enhets kod stub-filer.

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du kan använda Vcpkg-paketet för att skapa en genererad enhets kod stub. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en `cmake` under katalog i mappen `sample_device` och navigera till mappen:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade koden stub (Ersätt plats hållaren med katalogen för din Vcpkg-lagrings platsen):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de build-verktyg som du använder:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Om cmake inte kan hitta C++ din kompilator får du build-fel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)-Kommandotolken.

1. När skapandet har slutförts anger du DPS-autentiseringsuppgifterna (**DPS-ID-omfånget**, den **symmetriska DPS-nyckeln**, **enhets-ID**) som parametrar för programmet. Information om hur du hämtar autentiseringsuppgifterna från certifierings portalen finns i [ansluta och testa din IoT plug and Play-enhet](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementera standard gränssnitt

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementera modell information och gränssnitt för SDK-information

Azure IoT-enhetens SDK implementerar modell informationen och SDK-informations gränssnitten. Om du använder funktionen för kodgenerering i VS Code använder enhets koden IoT Plug and Play Device SDK.

Om du väljer att inte använda Azure IoT-enhetens SDK kan du använda SDK-källkod som referens för din egen implementering.

#### <a name="implement-the-device-information-interface"></a>Implementera enhets informations gränssnittet

Implementera **enhets informations** gränssnittet på enheten och ange enhets information från enheten vid körning.

Du kan använda en exempel implementering av **enhets informations** gränssnittet för [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) som referens.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementera alla funktioner som definierats i din modell

Under certifieringen testas enheten program mässigt för att säkerställa att den implementerar funktioner som definierats i dess gränssnitt. Använd HTTP-statuskod 501 för att svara på Read-Write-egenskap och kommando begär Anden om enheten inte implementerar dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en IoT Plug and Play-enhet redo för certifiering är det föreslagna nästa steg att:

> [!div class="nextstepaction"]
> [Lär dig hur du certifierar din enhet](tutorial-certification-test.md)
