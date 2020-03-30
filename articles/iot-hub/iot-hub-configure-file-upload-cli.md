---
title: Konfigurera filöverföring till IoT Hub med Azure CLI | Microsoft-dokument
description: Konfigurera filöverföringar till Azure IoT Hub med azure CLI.How to configure file uploads to Azure IoT Hub using the cross-platform Azure CLI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302533"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurera IoT Hub-filöverföringar med Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Om du vill [överföra filer från en enhet](iot-hub-devguide-file-upload.md)måste du först associera ett Azure Storage-konto med din IoT-hubb. Du kan använda ett befintligt lagringskonto eller skapa ett nytt.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* En Azure IoT-hubb. Om du inte har en IoT-hubb kan du använda [ `az iot hub create` kommandot](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) för att skapa en eller [Skapa en IoT-hubb med hjälp av portalen](iot-hub-create-through-portal.md).

* Ett Azure Storage-konto. Om du inte har ett Azure Storage-konto kan du använda Azure CLI för att skapa ett. Mer information finns i [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. I kommandotolken kör du [inloggningskommandot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer får du åtkomst till alla Azure-konton som är associerade med dina autentiseringsuppgifter när du loggar in på Azure. Använd följande [-kommando för att lista Azure-konton](https://docs.microsoft.com/cli/azure/account) som du kan använda:

    ```azurecli
    az account list
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta dina lagringskontouppgifter

Följande steg förutsätter att du har skapat ditt lagringskonto med hjälp av **Resurshanterarens** distributionsmodell och inte den **klassiska** distributionsmodellen.

Om du vill konfigurera filöverföringar från dina enheter behöver du anslutningssträngen för ett Azure-lagringskonto. Lagringskontot måste finnas i samma prenumeration som din IoT-hubb. Du behöver också namnet på en blob-behållare i lagringskontot. Använd följande kommando för att hämta dina lagringskontonycklar:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Anteckna värdet för **connectionString.** Du behöver det i följande steg.

Du kan antingen använda en befintlig blob-behållare för filöverföringar eller skapa en ny:

* Om du vill visa de befintliga blob-behållarna i ditt lagringskonto använder du följande kommando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Om du vill skapa en blob-behållare i ditt lagringskonto använder du följande kommando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Ladda upp filer

Du kan nu konfigurera din IoT-hubb för att aktivera möjligheten att [ladda upp filer till IoT-hubben](iot-hub-devguide-file-upload.md) med hjälp av dina lagringskontouppgifter.

Konfigurationen kräver följande värden:

* **Lagringsbehållare**: En blob-behållare i ett Azure-lagringskonto i din aktuella Azure-prenumeration för att associera med din IoT-hubb. Du har hämtat nödvändig lagringskontoinformation i föregående avsnitt. IoT Hub genererar automatiskt SAS-urier med skrivbehörighet till den här blob-behållaren för enheter som ska användas när de laddar upp filer.

* **Ta emot meddelanden för uppladdade filer**: Aktivera eller inaktivera meddelanden om filöverföring.

* **SAS TTL**: Den här inställningen är tid att leva på SAS-URI:er som returneras till enheten av IoT Hub. Ställ in på en timme som standard.

* **Filmeddelandeinställningar standard TTL:** Tid till live för en filuppladdning meddelande innan den har löpt ut. Ställ in på en dag som standard.

* **Antal maximala leveransleveranser**för filmeddelanden : Antalet gånger IoT Hub försöker leverera ett meddelande om filöverföring. Ange till 10 som standard.

Använd följande Azure CLI-kommandon för att konfigurera inställningarna för filöverföring på din IoT-hubb:

<!--Robinsh this is out of date, add cloud powershell -->

I ett bash skal, använd:

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

Du kan granska filöverföringskonfigurationen på IoT-hubben med följande kommando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om filöverföringsfunktionerna i IoT Hub finns i [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md).

Följ de här länkarna om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Säkra din IoT-lösning från grunden](../iot-fundamentals/iot-security-ground-up.md)
