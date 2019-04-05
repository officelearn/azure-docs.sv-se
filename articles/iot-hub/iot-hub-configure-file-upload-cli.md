---
title: Konfigurera filuppladdning till IoT Hub med Azure CLI | Microsoft Docs
description: Så här konfigurerar du fil laddas upp till Azure IoT Hub med hjälp av plattformsoberoende Azure CLI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: fe6ce23b9e87235521739b7808712a9d541dabf9
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048970"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurera IoT Hub-filöverföringar med hjälp av Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Att [ladda upp filer från en enhet](iot-hub-devguide-file-upload.md), måste du först associera ett Azure Storage-konto med IoT-hubben. Du kan använda ett befintligt lagringskonto eller skapa en ny.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Azure IoT hub. Om du inte har en IoT-hubb, kan du använda den [ `az iot hub create` kommandot](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) att skapa en eller [skapar en IoT hub med hjälp av portalen](iot-hub-create-through-portal.md).

* Ett Azure Storage-konto. Om du inte har ett Azure Storage-konto kan du använda den [Azure CLI – hantera lagringskonton](../storage/common/storage-azure-cli.md#manage-storage-accounts) att skapa något eller använder portalen för att [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. I Kommandotolken, kör den [inloggningskommandot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Använd följande [kommando för att lista Azure-konton](https://docs.microsoft.com/cli/azure/account) som du kan använda:

    ```azurecli
    az account list
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandon för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om ditt lagringskonto

Följande steg förutsätter att du har skapat ditt storage-konto med den **Resource Manager** -distributionsmodellen och inte den **klassiska** distributionsmodell.

Om du vill konfigurera filöverföringar från dina enheter, behöver du anslutningssträngen för ett Azure storage-konto. Lagringskontot måste vara i samma prenumeration som din IoT-hubb. Du måste också namnet på en blob-behållare i lagringskontot. Använd följande kommando för att hämta dina lagringskontonycklar:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Anteckna den **connectionString** värde. Du behöver det i följande steg.

Du kan använda en befintlig blobbehållare för dina filöverföringar, eller så kan du skapa en ny:

* Om du vill visa befintliga blobbehållare i ditt storage-konto, använder du följande kommando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Om du vill skapa en blobbehållare i ditt storage-konto, använder du följande kommando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Filuppladdning

Nu kan du konfigurera din IoT-hubb för att göra möjligheten att [ladda upp filer till IoT hub](iot-hub-devguide-file-upload.md) med information om ditt lagringskonto.

Konfigurationen kräver följande värden:

* **Lagringsbehållare**: En blob-behållare i ett Azure storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Du har hämtat den nödvändiga lagringskontoinformation i föregående avsnitt. IoT Hub genererar automatiskt SAS URI: er med skrivbehörighet till den här blob-behållare för enheter som ska användas när de laddar upp filer.

* **Ta emot meddelanden om uppladdade filer**: Aktivera eller inaktivera meddelanden för uppladdning av filen.

* **SAS TTL**: Den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT Hub. Ange en timme som standard.

* **Filen notification inställningar standard TTL**: De time-to-live för en fil ladda upp meddelandet innan det förfaller. Ange en dag som standard.

* **Filen notification maximalt antal leveranser**: Hur många gånger som IoT-hubben försöker leverera en fil att överföra meddelanden. Värdet 10 som standard.

Använd följande Azure CLI-kommandon för att ange inställningarna för ladda upp filen på din IoT-hubb:

<!--Robinsh this is out of date, add cloud powershell -->

Använd i ett bash-gränssnitt:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Du kan granska filen ladda upp konfigurationen på IoT hub med hjälp av följande kommando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om funktionerna du kan ladda upp filen för IoT Hub, [ladda upp filer från en enhet](iot-hub-devguide-file-upload.md).

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub metrics](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Skydda din IoT-lösning från grunden upp](../iot-fundamentals/iot-security-ground-up.md)
