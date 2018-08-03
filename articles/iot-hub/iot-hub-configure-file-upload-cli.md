---
title: Konfigurera filuppladdning till IoT Hub med Azure CLI (az.py) | Microsoft Docs
description: Så här konfigurerar du filöverföringar till Azure IoT Hub med hjälp av plattformsoberoende Azure CLI 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0eac620d44967827f7703da9cf409703a123ab07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460200"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurera IoT Hub-filöverföringar med hjälp av Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Du använder den [filen filuppladdning i IoT Hub][lnk-upload], måste du först associera ett Azure Storage-konto med IoT-hubben. Du kan använda ett befintligt lagringskonto eller skapa en ny.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure CLI 2.0][lnk-CLI-install].
* Azure IoT hub. Om du inte har en IoT-hubb, kan du använda den `az iot hub create` [kommandot] [ lnk-cli-create-iothub] att skapa något eller använda portalen för att [skapa en IoT-hubb] [lnk-portal-hub].
* Ett Azure Storage-konto. Om du inte har ett Azure Storage-konto kan du använda den [Azure CLI 2.0 - hantera lagringskonton] [ lnk-manage-storage] att skapa något eller använder portalen för att [skapa ett lagringskonto] [ lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. I kommandotolken kör du [inloggningskommandot][lnk-login-command]:

    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

1. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Använd följande [-kommando för att lista Azure-konton][lnk-az-account-command] som du kan använda:

    ```azurecli
    az account list
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om ditt lagringskonto

Följande steg förutsätter att du har skapat ditt storage-konto med den **Resource Manager** -distributionsmodellen och inte den **klassiska** distributionsmodell.

Om du vill konfigurera filöverföringar från dina enheter, behöver du anslutningssträngen för ett Azure storage-konto. Lagringskontot måste vara i samma prenumeration som din IoT-hubb. Du måste också namnet på en blob-behållare i lagringskontot. Använd följande kommando för att hämta dina lagringskontonycklar:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Anteckna den **connectionString** värde. Du behöver det i följande steg.

Du kan använda en befintlig blobbehållare för dina filöverföringar, eller så kan du skapa ny:

* Om du vill visa befintliga blobbehållare i ditt storage-konto, använder du följande kommando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Om du vill skapa en blobbehållare i ditt storage-konto, använder du följande kommando:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Filuppladdning

Nu kan du konfigurera din IoT-hubb för att aktivera [filen filuppladdning] [ lnk-upload] med information om ditt lagringskonto.

Konfigurationen kräver följande värden:

**Lagringsbehållare**: en blobbehållare i Azure storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Du har hämtat den nödvändiga lagringskontoinformation i föregående avsnitt. IoT Hub genererar automatiskt SAS URI: er med skrivbehörighet till den här blob-behållare för enheter som ska användas när de laddar upp filer.

**Ta emot meddelanden om uppladdade filer**: aktivera eller inaktivera meddelanden för uppladdning av filen.

**SAS TTL**: den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT Hub. Ange en timme som standard.

**Filen notification inställningar standard TTL**: den time-to-live för en fil ladda upp meddelandet innan det förfaller. Ange en dag som standard.

**Filen notification maximalt antal leveranser**: antalet gånger som IoT-hubben försöker leverera en fil ladda upp meddelandet. Värdet 10 som standard.

Använd följande Azure CLI-kommandon för att ange inställningarna för ladda upp filen på din IoT-hubb:

I en bash-gränssnittet använder:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Använd en Windows-kommandotolk:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Du kan granska filen ladda upp konfigurationen på IoT hub med hjälp av följande kommando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om funktionerna du kan ladda upp filen för IoT Hub, [ladda upp filer från en enhet][lnk-upload].

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter][lnk-bulk]
* [IoT Hub-mått][lnk-metrics]
* [Åtgärdsövervakning][lnk-monitor]

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]
* [Skydda din IoT-lösning från grunden upp][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: /azure/iot-fundamentals/iot-security-ground-up


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create