---
title: Använd Azure IoT-tillägget för Azure CLI för att interagera med IoT Plug and Play Preview-enheter | Microsoft Docs
description: Installera Azure IoT-tillägget för Azure CLI och Använd det för att interagera med IoT-Plug and Play enheter som är anslutna till min IoT-hubb.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352360"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installera och använda Azure IoT-tillägget för Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommando rads verktyg med öppen källkod för flera plattformar för att hantera Azure-resurser som IoT Hub. Azure CLI är tillgängligt på Windows, Linux och MacOS. Azure CLI är också förinstallerat i [Azure Cloud Shell](https://shell.azure.com). Med Azure CLI kan du hantera Azure IoT Hub-resurser, enhets etablerings tjänst instanser och länkade hubbar utan att installera några tillägg.

Azure IoT-tillägget för Azure CLI är ett kommando rads verktyg för att interagera med och testa IoT Plug and Play Preview-enheter. Du kan använda tillägget för att:

- Anslut till en enhet.
- Visa den telemetri som enheten skickar.
- Arbeta med enhets egenskaper.
- Anropa enhets kommandon.

Den här artikeln visar hur du:

- Installera och konfigurera Azure IoT-tillägget för Azure CLI.
- Använd tillägget för att interagera med och testa dina enheter.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installera Azure IoT-tillägget för Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Steg 1 – installera Azure CLI

Följ [installations anvisningarna](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att konfigurera Azure CLI i din miljö. För bästa möjliga upplevelse bör Azure CLI-versionen vara version 2.9.1 eller senare. Validera med `az -–version`.

### <a name="step-2---install-iot-extension"></a>Steg 2 – Installera IoT-tillägg

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-iot` för att se information om IoT-tillägget. Vid tidpunkten för skrivning är tilläggets versions nummer `0.9.7` .

Om du vill ta bort tillägget kan du använda `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Använda Azure IoT-tillägget för Azure CLI

### <a name="prerequisites"></a>Krav

Logga in på din Azure-prenumeration genom att köra följande kommando:

```azurecli
az login
```

> [!NOTE]
> Om du använder Azure Cloud Shell loggas du automatiskt in och du behöver inte köra föregående kommando.

Om du vill använda Azure IoT-tillägget för Azure CLI behöver du:

- En Azure IoT-hubb. Det finns många sätt att lägga till en IoT-hubb i din Azure-prenumeration, till exempel [skapa en IoT-hubb med Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Du behöver IoT-hubbens anslutnings sträng för att köra Azure IoT Extension-kommandona. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- En enhet som är registrerad i IoT Hub. Du kan använda följande Azure CLI-kommando för att registrera en enhet, se till att ersätta `{YourIoTHubName}` `{YourDeviceID}` plats hållarna och med dina värden:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interagera med en enhet

Du kan använda tillägget för att visa och interagera med IoT Plug and Play enheter som är anslutna till en IoT-hubb. Tillägget fungerar med den digitala dubbla som representerar IoT Plug and Play-enheten.

#### <a name="list-devices"></a>Visa enheter

Lista alla enheter på en IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Visa Digital, dubbel

Visa en enhets digitala enhet:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Egenskaper

Ange värdet för en Read-Write-egenskap:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Kommandon

Anropa ett kommando:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Digitala dubbla händelser

Övervaka alla IoT Plug and Play digitala dubbla händelser från en speciell enhet och gränssnitt till **$default** Event Hub-konsument gruppen:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du installerar och använder Azure IoT-tillägget för Azure CLI för att interagera med dina Plug and Play-enheter. Ett förslag till nästa steg är att lära dig hur du använder [Azure IoT Explorer med dina enheter](./howto-use-iot-explorer.md).
