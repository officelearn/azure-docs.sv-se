---
title: "Konfigurera filöverföringen till IoT-hubb med Azure CLI (az.py) | Microsoft Docs"
description: "Hur du konfigurerar filöverföringar till Azure IoT Hub använder plattformsoberoende Azure CLI 2.0 (az.py)."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6b100e65aba604fd8becb02c3a205b3348872bc4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurera IoT-hubb filöverföringar med hjälp av Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Att använda den [filen överför funktioner i IoT-hubb][lnk-upload], måste du först associera ett Azure Storage-konto med din IoT-hubb. Du kan använda ett befintligt lagringskonto eller skapa en ny.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure CLI 2.0][lnk-CLI-install].
* Azure IoT-hubb. Om du inte har en IoT-hubb kan du använda den `az iot hub create` [kommandot] [ lnk-cli-create-iothub] att skapa något eller använda portalen för att [skapa en IoT-hubb] [lnk-portal-hubb].
* Ett Azure Storage-konto. Om du inte har ett Azure Storage-konto kan du använda den [Azure CLI 2.0 - lagringskonton hantera] [ lnk-manage-storage] att skapa något eller använder portalen för att [skapa ett lagringskonto] [ lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välja din prenumeration.

1. Vid Kommandotolken kör du den [inloggningen kommandot][lnk-login-command]:

    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

1. Om du har flera Azure-prenumerationer, ger logga in på Azure åtkomst till alla Azure konton som är associerade med dina autentiseringsuppgifter. Använd följande [kommando för att visa en lista med Azure-konton] [ lnk-az-account-command] som du kan använda:

    ```azurecli
    az account list
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan använda prenumerationsnamn eller ID från utdata från det föregående kommandot:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om ditt lagringskonto

Följande steg förutsätter att du har skapat din storage-konto med den **Resource Manager** distributionsmodellen, och inte den **klassiska** distributionsmodell.

Om du vill konfigurera filöverföringar från dina enheter, behöver du anslutningssträngen för Azure storage-konto. Lagringskontot måste vara i samma prenumeration som din IoT-hubb. Du måste också namnet på en blob-behållare i storage-konto. Använd följande kommando för att hämta dina nycklar för lagringskonto:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Anteckna den **connectionString** värde. Du behöver den i följande steg.

Du kan använda en befintlig blobbehållare för dina filöverföringar eller skapa nya:

* Om du vill visa en lista över de befintliga blob-behållarna i ditt lagringskonto, använder du följande kommando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Om du vill skapa en blob-behållare i storage-konto, använder du följande kommando:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Ladda upp filen

Du kan nu konfigurera din IoT-hubb för att aktivera [filen överför funktioner] [ lnk-upload] med information om ditt lagringskonto.

Konfigurationen kräver följande värden:

**Lagringsbehållaren**: en blob-behållare i ett Azure storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Du har hämtat informationen om behövs lagring i föregående avsnitt. IoT-hubb genererar automatiskt SAS URI: er med behörighet att skriva till blob-behållare för enheter som ska användas när de överför filer.

**Ta emot meddelanden för överförda filer**: aktivera eller inaktivera filen överför meddelanden.

**SAS TTL**: den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT-hubb. Ange till en timme som standard.

**Filen notification inställningar Standardbegränsning**: den time-to-live av en fil överför meddelande innan det förfaller. Ange en dag som standard.

**Meddelande leverans av maximalt antal filer**: antalet gånger som IoT-hubben försöker att leverera en fil överför meddelande. Satt till 10 som standard.

Använd följande kommandon för Azure CLI för att konfigurera överför inställningar på din IoT-hubb:

En bash shell användning:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

På en Windows kommandotolk användning:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Du kan granska filen överför konfiguration på din IoT-hubb med följande kommando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om funktionerna som överför filen IoT-hubb finns [överföra filer från en enhet][lnk-upload].

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [Massinläsning hantera IoT-enheter][lnk-bulk]
* [IoT-hubb mått][lnk-metrics]
* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]
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
[lnk-securing]: iot-hub-security-ground-up.md


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
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create