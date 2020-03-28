---
title: Skapa en IoT Plug and Play Preview-enhet som är klar för certifiering | Microsoft-dokument
description: Som enhetsutvecklare kan du läsa om hur du kan skapa en IoT Plug and Play Preview-enhet som är klar för certifiering.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239913"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Skapa en IoT Plug and Play Preview-enhet som är klar för certifiering

I den här självstudien beskrivs hur du som enhetsutvecklare kan skapa en IoT Plug and Play Preview-enhet som är klar för certifiering.

Certifieringstesterna kontrollerar att:

- IoT Plug and Play-enhetskoden är installerad på enheten.
- Din IoT Plug and Play-enhetskod är byggd med Azure IoT SDK.
- Enhetskoden stöder [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
- Enhetskoden implementerar gränssnittet för enhetsinformation.
- Kapacitetsmodellen och enhetskoden fungerar med IoT Central.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- [Visual Studio-kod](https://code.visualstudio.com/download)
- [Tilläggspaket för Azure IoT Tools för VS-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Du måste också slutföra [användningsmodellen Använd en enhetskapacitet för att skapa en snabbstart](quickstart-create-pnp-device-windows.md) för enheten för Windows. Snabbstarten visar hur du ställer in utvecklingsmiljön med Vcpkg och skapar ett exempelprojekt.

## <a name="store-a-capability-model-and-interfaces"></a>Lagra en kapacitetsmodell och gränssnitt

För IoT Plug and Play-enheter måste du skapa en kapacitetsmodell och gränssnitt som definierar enhetens funktioner som JSON-filer.

Du kan lagra dessa JSON-filer på tre olika platser:

- Den offentliga modelldatabasen.
- Ditt företagsmodellarkiv.
- På enheten.

För närvarande måste filerna lagras antingen i företagsmodelldatabasen eller i den offentliga modelldatabasen för att certifiera enheten.

## <a name="include-the-required-interfaces"></a>Inkludera de gränssnitt som krävs

Om du vill klara certifieringsprocessen måste du inkludera och implementera **gränssnittet för enhetsinformation** i din kapacitetsmodell. Detta gränssnitt har följande identifiering:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Om du har slutfört [snabbstarten: Använd en enhetskapacitetsmodell för att skapa en enhet](quickstart-create-pnp-device-windows.md)har du redan inkluderat gränssnittet **enhetsinformation** i din modell.

Om du vill inkludera **gränssnittet enhetsinformation** i enhetsmodellen `implements` lägger du till gränssnitts-ID:n i egenskapen för kapacitetsmodellen:

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

Så här visar du **gränssnittet enhetsinformation** i VS-kod:

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Ange **Plug and Play** och välj sedan kommandot **IoT Plug and Play Open Model Repository.** Välj **Öppna offentlig modelldatabas**. Den offentliga modelldatabasen öppnas i VS-kod.

1. I den offentliga modelldatabasen väljer du fliken **Gränssnitt,** väljer filterikonen och anger **Enhetsinformation** i filterfältet.

1. Om du vill skapa en lokal kopia av **gränssnittet för enhetsinformation** markerar du det i den filtrerade listan och väljer sedan **Hämta**. VS-kod visar gränssnittsfilen.

Så här visar du **gränssnittet enhetsinformation** med Azure CLI:

1. [Installera Azure IoT CLI-tillägget](howto-install-pnp-cli.md).

1. Använd följande Azure CLI-kommando för att visa ett gränssnitt med enhetsinformationsgränssnitts-ID:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Mer information finns i [Installera och använda Azure IoT-tillägget för Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Uppdatera enhetskod

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Aktivera enhetsetablering via DPS (Azure IoT Device Provisioning Service)

För att certifiera enheten måste den aktivera etablering via [DPS (Azure IoT Device Provisioning Service).](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) Om du vill lägga till möjligheten att använda DPS kan du generera C-koden stub i VS-kod. Följ de här stegen:

1. Öppna mappen med DCM-filen i VS-kod, använd **Ctrl+Skift+P** för att öppna kommandopaletten, ange **IoT Plug and Play**och välj **Generera Enhetskodsstub.**

1. Välj den DCM-fil som du vill använda för att generera enhetskoden stub.

1. Ange projektnamnet, till exempel **sample_device**. Det här är namnet på enhetsprogrammet.

1. Välj **ANSI C** som språk.

1. Välj **symmetrisk nyckel via DPS (Enhetsetableringstjänst)** som anslutningsmetod.

1. Välj **CMake Project i Windows** som projektmall.

1. Välj **Via Vcpkg** som sätt att inkludera enheten SDK.

1. VS-kod öppnar ett nytt fönster med genererade enhetskodssub-filer.

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du använder Vcpkg-paketet för att skapa den genererade enhetskoden stub. Programmet du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa `cmake` en underkatalog `sample_device` i mappen och navigera till den mappen:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade kodsuben (ersätter platshållaren med katalogen för din Vcpkg-repo):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de byggverktyg du använder:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Om cmake inte kan hitta din C++-kompilator får du byggfel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. När build har slutförts anger du DPS-autentiseringsuppgifterna (**DPS ID Scope**, **DPS Symmetric Key**, **Device Id**) som parametrar för programmet. Information om hur du hämtar autentiseringsuppgifterna från certifieringsportalen finns i [Anslut och testa din IoT Plug and Play-enhet](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementera standardgränssnitt

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementera gränssnitten för modellinformation och SDK-information

Azure IoT-enheten SDK implementerar gränssnitten modellinformation och SDK-information. Om du använder kodgenereringsfunktionen i VS-kod använder enhetskoden IoT Plug and Play-enheten SDK.

Om du väljer att inte använda Azure IoT-enheten SDK kan du använda SDK-källkoden som referens för din egen implementering.

#### <a name="implement-the-device-information-interface"></a>Implementera gränssnittet för enhetsinformation

Implementera **gränssnittet för enhetsinformation** på enheten och ange enhetsspecifik information från enheten vid körning.

Du kan använda en exempelimplementering av **enhetsinformationsgränssnittet** för [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) som referens.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementera alla funktioner som definieras i din modell

Under certifieringen testas enheten programmässigt för att säkerställa att den implementerar funktioner som definierats i dess gränssnitt. Använd HTTP-statuskod 501 för att svara på läs-skriv-egenskap och kommandobegäranden om enheten inte implementerar dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har byggt en IoT Plug and Play-enhet redo för certifiering är det föreslagna nästa steget att:

> [!div class="nextstepaction"]
> [Läs om hur du certifierar enheten](tutorial-certification-test.md)
