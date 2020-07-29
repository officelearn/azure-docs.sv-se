---
title: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning (python) | Microsoft Docs
description: Använd python för att ansluta till och interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87353006"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Snabb start: interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning (python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play Preview fören klar IoT genom att göra det möjligt att interagera med en enhets modell utan kunskaper om den underliggande enhets implementeringen. Den här snabb starten visar hur du använder python för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du python 3,7 på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/). Du kan kontrol lera din python-version med följande kommando:  

```cmd/sh
python --version
```

Installera [python service SDK Preview-paketet](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) genom att köra följande kommando:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten använder du en exempel termostat-enhet som skrivits i python som IoT Plug and Play-enhet. Så här kör du exempel enheten:

1. Öppna ett terminalfönster i valfri mapp. Kör följande kommando för att klona [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub-lagringsplatsen till den här platsen:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Det här terminalfönstret används som **enhetens** Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen */Azure-IoT-SDK-python/Azure-IoT-Device/samples/PnP* .

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör exempel termostat-enheten med följande kommando:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Dessa meddelanden indikerar att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här terminalen, du behöver den för att bekräfta att tjänst exemplet fungerar.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I den här snabb starten använder du en exempel IoT-lösning i python för att interagera med den exempel enhet som du nyss konfigurerade.

1. Öppna ett annat terminalfönster som ska användas som **tjänstens** Terminal. Service SDK är i för hands version, så du måste klona exemplen från en [förhands gransknings gren av python SDK: n](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Gå till mappen för den här klonade databas grenen och navigera till mappen */Azure-IoT-SDK-python/Azure-IoT-Hub/samples* .

1. Konfigurera miljövariabler för enhets-ID och _IoT Hub anslutnings sträng_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. I mappen Samples finns fyra exempelfiler med ett `pnp` prefix. De här exemplen visar hur du använder varje API för att interagera med IoT Plug and Play-enheter:

### <a name="get-digital-twin"></a>Skaffa digital, dubbel

Använd följande kommando i **service-** terminalen för att köra det här exemplet:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

Utdata visar enhetens digitala kontakt och skriver ut dess modell-ID:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

I följande kodfragment visas exempel koden från *pnp_get_digital_twin_sample. py*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Uppdatera en digital delad

Det här exemplet visar hur du använder en *korrigering* för att uppdatera egenskaper via enhetens digitala enhet. Följande kodfragment från *pnp_update_digital_twin_sample. py* visar hur du skapar korrigerings filen:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Använd följande kommando i **service-** terminalen för att köra det här exemplet:

```cmd/sh
python pnp_update_digital_twin_sample.py
```

Du kan kontrol lera att uppdateringen används i **enhetens** Terminal som visar följande utdata:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

**Tjänsten** Terminal bekräftar att korrigeringen lyckades:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Anropa ett kommando

Anropa ett kommando genom att köra exemplet *pnp_invoke_command_sample. py* . Det här exemplet visar hur du anropar ett kommando på en enkel termostat-enhet. Innan du kör det här exemplet ställer du `IOTHUB_COMMAND_NAME` in `IOTHUB_COMMAND_PAYLOAD` miljövariablerna och i **tjänsten** terminalen:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

I **tjänsten** Terminal använder du följande kommando för att köra exemplet:
  
```cmd/sh
python pnp_invoke_command_sample.py
```

**Tjänsten** Terminal visar ett bekräftelse meddelande från enheten:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

I **enhetens** Terminal ser du att enheten tar emot kommandot:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Preview Modeling Developer Guide](concepts-developer-guide.md)
