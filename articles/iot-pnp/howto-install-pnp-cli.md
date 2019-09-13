---
title: Använd Azure IoT-tillägget för Azure CLI för att interagera med IoT Plug and Play Preview-enheter | Microsoft Docs
description: Installera Azure IoT-tillägget för Azure CLI och Använd det för att interagera med IoT-Plug and Play enheter som är anslutna till min IoT-hubb.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 41a626ba602ad33f22c3ea4acc39dd4f3438cbd0
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935695"
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
- Använd tillägget för att hantera gränssnitt i modell databasen.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installera Azure IoT-tillägget för Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Steg 1 – installera Azure CLI

Följ [installations anvisningarna](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att konfigurera Azure CLI i din miljö. Om du vill använda alla kommandon nedan måste din Azure CLI-version vara version 2.0.73 eller senare. Validera med `az -–version`.

### <a name="step-2---install-iot-extension"></a>Steg 2 – Installera IoT-tillägg

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-cli-iot-ext`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-cli-iot-ext` för att se information om IoT-tillägget. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-cli-iot-ext`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Använda Azure IoT-tillägget för Azure CLI

### <a name="prerequisites"></a>Förutsättningar

Logga in på din Azure-prenumeration genom att köra följande kommando:

```cmd/sh
az login
```

> [!NOTE]
> Om du använder Azure Cloud Shell loggas du automatiskt in och du behöver inte köra föregående kommando.

Om du vill använda Azure IoT-tillägget för Azure CLI behöver du:

- En Azure IoT-hubb. Det finns många sätt att lägga till en IoT-hubb i din Azure-prenumeration, till exempel [skapa en IoT-hubb med Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Du behöver IoT-hubbens anslutnings sträng för att köra Azure IoT Extension-kommandona. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

    > [!NOTE]
    > Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

- En enhet som är registrerad i IoT Hub. Du kan använda följande Azure CLI-kommando för att registrera en enhet, se till att ersätta `{YourIoTHubName}` plats `{YourDeviceID}` hållarna och med dina värden:

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Vissa kommandon behöver anslutnings strängen för en företags modell databas. Ett modell lager för ditt företag skapas när du först registrerar dig [på Azure-certifierad för IoT-portalen](howto-onboard-portal.md). En tredje part kan dela sin anslutnings sträng för modell databasen med dig för att ge dig åtkomst till gränssnitt och modeller.

### <a name="interact-with-a-device"></a>Interagera med en enhet

Du kan använda tillägget för att visa och interagera med IoT Plug and Play enheter som är anslutna till en IoT-hubb. Tillägget fungerar med den digitala dubbla som representerar IoT Plug and Play-enheten.

#### <a name="list-devices-and-interfaces"></a>Lista enheter och gränssnitt

Lista alla enheter på en IoT Hub:

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Lista alla gränssnitt som registrerats av en IoT Plug and Play-enhet:

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>properties

Lista alla egenskaper och egenskaps värden för ett gränssnitt på en enhet:

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Ange värdet för en Read-Write-egenskap:

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Ett exempel på en nytto Last fil som anger **namn** egenskapen på en enhets **sensor** gränssnitt till **contoso** ser ut så här:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Kommandon

Visa en lista med alla kommandon för ett gränssnitt på en enhet:

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

`--repo-login` Utan parametern använder det här kommandot den offentliga modellens lagrings plats.

Anropa ett kommando:

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --command-name {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="telemetry"></a>Telemetri

Övervaka alla IoT Plug and Play telemetri från en speciell enhet och gränssnitt som ska gå till **$default** Event Hub-slutpunkten:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Övervaka alla IoT Plug and Play telemetri från en speciell enhet och gränssnitt som går till en speciell konsument grupp:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Hantera gränssnitt i en modell databas

Följande kommandon använder den offentliga IoT Plug and Play Model-lagringsplatsen. Om du vill använda en företags modell databas lägger `--login` du till argumentet med anslutnings strängen för modell databasen.

Visa en lista över gränssnitt i den offentliga IoT-Plug and Play modell databasen:

```cmd/sh
az iot pnp interface list
```

Visa ett gränssnitt i den offentliga IoT-Plug and Play modell databasen:

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

Skapa ett gränssnitt i IoT-Plug and Play företagets modell databas:

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte skapa ett gränssnitt direkt i den offentliga modellens lagrings plats.

Uppdatera ett gränssnitt i IoT-Plug and Play företagets modell databas:

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte uppdatera ett gränssnitt direkt i den offentliga modellens lagrings plats.

Publicera ett gränssnitt från IoT-Plug and Play företagets modell databas till den offentliga modellens lagrings plats. Den här åtgärden gör gränssnittet oföränderligt:

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Endast Microsoft-partner kan publicera gränssnitt till den offentliga modellens lagrings plats.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Hantera enhets kapacitets modeller i en modell databas

Följande kommandon använder den offentliga IoT Plug and Play Model-lagringsplatsen. Om du vill använda en företags modell databas lägger `--login` du till argumentet med anslutnings strängen för modell databasen.

Visa lista över enhets kapacitets modeller i IoT Plug and Play offentliga modell databasen:

```cmd/sh
az iot pnp capability-model list
```

Visa en enhets kapacitets modell i IoT Plug and Play offentliga modell databasen:

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

Skapa en enhets kapacitets modell i en IoT Plug and Play företags modell databas:

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte skapa en modell direkt i den offentliga modellens lagrings plats.

Uppdatera en enhets kapacitets modell i IoT Plug and Play företags modellens lagrings plats:

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte uppdatera en modell direkt i den offentliga modellens lagrings plats.

Publicera en enhets kapacitets modell från IoT Plug and Play företagets modell databas till den offentliga modellens lagrings plats. Den här åtgärden gör modellen oföränderlig:

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Endast Microsoft-partner kan publicera modeller till den offentliga modellens lagrings plats.

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du installerar och använder Azure IoT-tillägget för Azure CLI för att interagera med dina Plug and Play-enheter. Ett förslag till nästa steg är att lära dig hur du [hanterar modeller](./howto-manage-models.md).
