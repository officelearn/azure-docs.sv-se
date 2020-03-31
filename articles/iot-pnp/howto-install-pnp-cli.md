---
title: Använda Azure IoT-tillägget för Azure CLI för att interagera med IoT Plug and Play Preview-enheter | Microsoft-dokument
description: Installera Azure IoT-tillägget för Azure CLI och använd det för att interagera med IoT Plug and Play-enheter som är anslutna till min IoT-hubb.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234688"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installera och använda Azure IoT-tillägget för Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med flera plattformar med öppen källkod för hantering av Azure-resurser som IoT Hub. Azure CLI är tillgängligt på Windows, Linux och MacOS. Azure CLI är också förinstallerat i [Azure Cloud Shell](https://shell.azure.com). Med Azure CLI kan du hantera Azure IoT Hub-resurser, instanser av enhetsetableringstjänst och länkade hubbar utan att installera några tillägg.

Azure IoT-tillägget för Azure CLI är ett kommandoradsverktyg för att interagera med och testa IoT Plug and Play Preview-enheter. Du kan använda tillägget för att:

- Anslut till en enhet.
- Visa den telemetri som enheten skickar.
- Arbeta med enhetsegenskaper.
- Anropa enhetskommandon.

Den här artikeln visar hur du:

- Installera och konfigurera Azure IoT-tillägget för Azure CLI.
- Använd tillägget för att interagera med och testa dina enheter.
- Använd tillägget för att hantera gränssnitt i modelldatabasen.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installera Azure IoT-tillägg för Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Steg 1 – Installera Azure CLI

Följ [installationsanvisningarna](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att konfigurera Azure CLI i din miljö. Om du vill använda alla kommandon nedan måste din Azure CLI-version vara version 2.0.73 eller högre. Validera med `az -–version`.

### <a name="step-2---install-iot-extension"></a>Steg 2 - Installera IoT-tillägg

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-iot` för att se information om IoT-tillägget. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Använda Azure IoT-tillägg för Azure CLI

### <a name="prerequisites"></a>Krav

Om du vill logga in på din Azure-prenumeration kör du följande kommando:

```azurecli
az login
```

> [!NOTE]
> Om du använder Azure-molnskalet loggas du automatiskt in och behöver inte köra föregående kommando.

Om du vill använda Azure IoT-tillägget för Azure CLI behöver du:

- En Azure IoT-hubb. Det finns många sätt att lägga till en IoT-hubb i din Azure-prenumeration, till exempel [Skapa en IoT-hubb med Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Du behöver IoT-hubbens anslutningssträng för att köra Azure IoT-tilläggskommandona. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

- En enhet som är registrerad i din IoT-hubb. Du kan använda följande Azure CLI-kommando för att `{YourIoTHubName}` registrera `{YourDeviceID}` en enhet, se till att ersätta och platshållarna med dina värden:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Vissa kommandon behöver anslutningssträngen för en företagsmodelldatabas. En modelldatabas för ditt företag skapas när du först [gick ombord till Azure Certified for IoT-portalen](howto-onboard-portal.md). En tredje part kan dela sin anslutningssträng för modelllagringsplats med dig för att ge dig tillgång till deras gränssnitt och modeller.

### <a name="interact-with-a-device"></a>Interagera med en enhet

Du kan använda tillägget för att visa och interagera med IoT Plug and Play-enheter som är anslutna till en IoT-hubb. Tillägget fungerar med den digitala tvillingen som representerar IoT Plug and Play-enheten.

#### <a name="list-devices-and-interfaces"></a>Lista enheter och gränssnitt

Lista alla enheter på en IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Lista alla gränssnitt som registrerats av en IoT Plug and Play-enhet:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Egenskaper

Lista alla egenskaper och egenskapsvärden för ett gränssnitt på en enhet:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Ange värdet för en läs-skriv-egenskap:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

En exempeluppladdningsfil **name** för att ange namnegenskapen på **sensorgränssnittet** för en enhet till **Contoso** ser ut så här:

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

Lista alla kommandon för ett gränssnitt på en enhet:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Utan `--repo-login` parametern använder det här kommandot den offentliga modelldatabasen.

Anropa ett kommando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Digitala tvillingevenemang

Övervaka alla IoT Plug and Play digitala tvillinghändelser från en specifik enhet och ett visst gränssnitt som går till **konsumentgruppen för $Default** händelsehubben:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Övervaka alla IoT Plug and Play digitala tvillinghändelser från en viss enhet och ett specifikt gränssnitt och gå till en viss konsumentgrupp:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Hantera gränssnitt i en modelldatabas

Följande kommandon använder den offentliga IoT Plug and Play-modelldatabasen. Om du vill använda en `--login` företagsmodelldatabas lägger du till argumentet med anslutningssträngen för modelldatabasen.

Lista gränssnitt i den offentliga IoT Plug and Play-modellens databas:

```azurecli
az iot pnp interface list
```

Visa ett gränssnitt i den offentliga IoT Plug and Play-modellens databas:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Skapa ett gränssnitt i ioT Plug and Play-företagsmodellarkivet:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte skapa ett gränssnitt direkt i den offentliga modelldatabasen.

Uppdatera ett gränssnitt i ioT Plug and Play-företagsmodellarkivet:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte uppdatera ett gränssnitt direkt i den offentliga modelldatabasen.

Publicera ett gränssnitt från ioT Plug and Play-företagsmodellarkivet i den offentliga modelldatabasen. Den här åtgärden gör gränssnittet oföränderligt:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Endast Microsoft-partner kan publicera gränssnitt till den offentliga modelldatabasen.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Hantera enhetskapacitetsmodeller i en modelldatabas

Följande kommandon använder den offentliga IoT Plug and Play-modelldatabasen. Om du vill använda en `--login` företagsmodelldatabas lägger du till argumentet med anslutningssträngen för modelldatabasen.

Lista enhetsfunktioner modeller i IoT Plug and Play offentliga modellarkivet:

```azurecli
az iot pnp capability-model list
```

Visa en enhetskapacitetsmodell i ioT Plug and Play offentliga modellarkivet:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Skapa en enhetskapacitetsmodell i en IoT Plug and Play-företagsmodellarkiv:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte skapa en modell direkt i den offentliga modelldatabasen.

Uppdatera en enhetskapacitetsmodell i IoT Plug and Play-företagsmodellarkivet:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Du kan inte uppdatera en modell direkt i den offentliga modelldatabasen.

Publicera en enhetskapacitetsmodell från ioT Plug and Play-företagsmodellarkivet till den offentliga modelldatabasen. Den här åtgärden gör modellen oföränderlig:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Endast Microsoft-partner kan publicera modeller i den offentliga modelldatabasen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du installerar och använder Azure IoT-tillägget för Azure CLI för att interagera med dina Plug and Play-enheter. Ett förslag på nästa steg är att lära sig [att hantera modeller](./howto-manage-models.md).
