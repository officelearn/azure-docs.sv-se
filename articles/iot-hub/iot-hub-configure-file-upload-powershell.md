---
title: Använd Azure PowerShell för att konfigurera filöverföring | Microsoft-dokument
description: Så här använder du Azure PowerShell-cmdletar för att konfigurera IoT-hubben för att aktivera filöverföringar från anslutna enheter. Innehåller information om hur du konfigurerar azure-lagringskontot för målet.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60318468"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurera uppladdningar av IoT Hub-filer med PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Om du vill använda [filöverföringsfunktionen i IoT Hub](iot-hub-devguide-file-upload.md)måste du först associera ett Azure-lagringskonto med din IoT-hubb. Du kan använda ett befintligt lagringskonto eller skapa ett nytt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* En Azure IoT-hubb. Om du inte har en IoT-hubb kan du använda [cmdleten New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) för att skapa en eller använda portalen för att [skapa en IoT-hubb](iot-hub-create-through-portal.md).

* Ett Azure-lagringskonto. Om du inte har ett Azure-lagringskonto kan du använda [Azure Storage PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.storage/) för att skapa ett eller använda portalen för att [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. Kör cmdleten **Connect-AzAccount** i PowerShell-prompten:

    ```powershell
    Connect-AzAccount
    ```

2. Om du har flera Azure-prenumerationer ger inloggning till Azure dig åtkomst till alla Azure-prenumerationer som är associerade med dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationer som är tillgängliga för dig att använda:

    ```powershell
    Get-AzSubscription
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att hantera din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta dina lagringskontouppgifter

Följande steg förutsätter att du har skapat ditt lagringskonto med hjälp av **Resurshanterarens** distributionsmodell och inte den **klassiska** distributionsmodellen.

Om du vill konfigurera filöverföringar från dina enheter behöver du anslutningssträngen för ett Azure-lagringskonto. Lagringskontot måste finnas i samma prenumeration som din IoT-hubb. Du behöver också namnet på en blob-behållare i lagringskontot. Använd följande kommando för att hämta dina lagringskontonycklar:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anteckna nyckelvärdet för key1-lagringskontot. **key1** Du behöver det i följande steg.

Du kan antingen använda en befintlig blob-behållare för filöverföringar eller skapa en ny:

* Om du vill visa de befintliga blob-behållarna i ditt lagringskonto använder du följande kommandon:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Om du vill skapa en blob-behållare i ditt lagringskonto använder du följande kommandon:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurera din IoT-hubb

Du kan nu konfigurera IoT-hubben för att [ladda upp filer till IoT-hubben](iot-hub-devguide-file-upload.md) med hjälp av dina lagringskontouppgifter.

Konfigurationen kräver följande värden:

* **Lagringsbehållare**: En blob-behållare i ett Azure-lagringskonto i din aktuella Azure-prenumeration för att associera med din IoT-hubb. Du har hämtat nödvändig lagringskontoinformation i föregående avsnitt. IoT Hub genererar automatiskt SAS-urier med skrivbehörighet till den här blob-behållaren för enheter som ska användas när de laddar upp filer.

* **Ta emot meddelanden för uppladdade filer**: Aktivera eller inaktivera meddelanden om filöverföring.

* **SAS TTL**: Den här inställningen är tid att leva på SAS-URI:er som returneras till enheten av IoT Hub. Ställ in på en timme som standard.

* **Filmeddelandeinställningar standard TTL:** Tid till live för en filuppladdning meddelande innan den har löpt ut. Ställ in på en dag som standard.

* **Antal maximala leveransleveranser**för filmeddelanden : Antalet gånger IoT Hub försöker leverera ett meddelande om filöverföring. Ange till 10 som standard.

Använd följande PowerShell-cmdlet för att konfigurera inställningarna för filöverföring på IoT-hubben:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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
