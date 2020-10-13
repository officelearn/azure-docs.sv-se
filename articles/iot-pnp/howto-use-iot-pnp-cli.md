---
title: Använd Azure IoT-tillägget för Azure CLI för att interagera med IoT Plug and Play-enheter | Microsoft Docs
description: Installera Azure IoT-tillägget för Azure CLI och Använd det för att interagera med IoT-Plug and Play enheter som är anslutna till min IoT-hubb.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577466"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installera och använda Azure IoT-tillägget för Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) är ett kommando rads verktyg med öppen källkod för flera plattformar för att hantera Azure-resurser som IoT Hub. Azure CLI är tillgängligt på Windows, Linux och macOS. Med Azure CLI kan du hantera Azure IoT Hub-resurser, enhets etablerings tjänst instanser och länkade hubbar utan att installera några tillägg.

Azure IoT-tillägget för Azure CLI är ett kommando rads verktyg för att interagera med och testa IoT Plug and Play-enheter. Du kan använda tillägget för att:

- Anslut till en enhet.
- Visa den telemetri som enheten skickar.
- Arbeta med enhets egenskaper.
- Anropa enhets kommandon.

Den här artikeln visar hur du:

- Installera och konfigurera Azure IoT-tillägget för Azure CLI.
- Använd tillägget för att interagera med och testa dina enheter.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installera Azure IoT-tillägget för Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Steg 1 – installera Azure CLI

Följ [installations anvisningarna](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) för att konfigurera Azure CLI i din miljö. För bästa möjliga upplevelse bör Azure CLI-versionen vara version 2.9.1 eller senare. Validera med `az -–version`.

### <a name="step-2---install-iot-extension"></a>Steg 2 – Installera IoT-tillägg

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-iot` för att se information om IoT-tillägget. Vid tidpunkten för skrivning är tilläggets versions nummer `0.10.0` .

Om du vill ta bort tillägget kan du använda `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Använda Azure IoT-tillägget för Azure CLI

### <a name="prerequisites"></a>Förutsättningar

Logga in på din Azure-prenumeration genom att köra följande kommando:

```azurecli
az login
```

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

### <a name="manage-models-in-the-model-repository"></a>Hantera modeller i modell databasen

Du kan använda databas kommandona för Azure CLI-modellen för att hantera modeller i lagrings platsen.

#### <a name="create-model-repository"></a>Skapa modell databas

Skapa en ny IoT Plug and Play företags databas för din klient om du är den första användaren i din klient organisation:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Hantera klient organisations rollerna för modell databasen

Skapa en roll tilldelning för en användare eller tjänstens huvud namn till en angiven resurs.

Ge till exempel user@consoso.com rollen **ModelsCreator** för klienten:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Eller ge user@consoso.com rollen **ModelAdministrator** för en speciell modell:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Skapa en modell

Skapa en ny modell i företags databasen:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Sök i en modell

Lista modeller som matchar ett angivet nyckelord:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Publicera en modell

Publicera en enhets modell som finns på företags databasen till den offentliga lagrings platsen.

Gör till exempel offentlig modell med ID `dtmi:com:example:ClimateSensor;1` :

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

För att publicera en modell måste följande krav vara uppfyllda:

- Företaget eller organisationens klient organisation måste vara en Microsoft-partner. 
- Användarens eller tjänstens huvud namn måste vara medlem i plats innehavarens **utgivar** roll.

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du installerar och använder Azure IoT-tillägget för Azure CLI för att interagera med IoT Plug and Play-enheter. Ett förslag till nästa steg är att lära dig hur du använder [Azure IoT Explorer med dina enheter](./howto-use-iot-explorer.md).
