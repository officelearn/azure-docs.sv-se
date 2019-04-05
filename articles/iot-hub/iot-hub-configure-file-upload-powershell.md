---
title: Använda Azure PowerShell för att konfigurera filuppladdning | Microsoft Docs
description: Hur du använder Azure PowerShell-cmdlets för att konfigurera IoT hub för att aktivera filöverföringar från anslutna enheter. Innehåller information om hur du konfigurerar målet Azure storage-konto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4f1a5d59c340a02dcdc0291046ef6361c6f41c86
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044920"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurera IoT Hub-filöverföringar med hjälp av PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Du använder den [filen filuppladdning i IoT Hub](iot-hub-devguide-file-upload.md), måste du först associera ett Azure storage-konto med IoT-hubben. Du kan använda ett befintligt lagringskonto eller skapa en ny.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Azure IoT hub. Om du inte har en IoT-hubb, kan du använda den [cmdlet New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) att skapa något eller använder portalen för att [skapar en IoT hub](iot-hub-create-through-portal.md).

* Ett Azure-lagringskonto. Om du inte har ett Azure storage-konto kan du använda den [Azure Storage PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.storage/) att skapa något eller använder portalen för att [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. I PowerShell-Kommandotolken kör den **Connect AzAccount** cmdlet:

    ```powershell
    Connect-AzAccount
    ```

2. Om du har flera Azure-prenumerationer kan får logga in på Azure du åtkomst till alla Azure-prenumerationer som är associerade med dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

    ```powershell
    Get-AzSubscription
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandon för att hantera din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om ditt lagringskonto

Följande steg förutsätter att du har skapat ditt storage-konto med den **Resource Manager** -distributionsmodellen och inte den **klassiska** distributionsmodell.

Om du vill konfigurera filöverföringar från dina enheter, behöver du anslutningssträngen för ett Azure storage-konto. Lagringskontot måste vara i samma prenumeration som din IoT-hubb. Du måste också namnet på en blob-behållare i lagringskontot. Använd följande kommando för att hämta dina lagringskontonycklar:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anteckna den **key1** nyckelvärdet för storage-konto. Du behöver det i följande steg.

Du kan använda en befintlig blobbehållare för dina filöverföringar, eller så kan du skapa ny:

* Om du vill visa befintliga blobbehållare i ditt storage-konto, använder du följande kommandon:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Om du vill skapa en blobbehållare i ditt storage-konto, använder du följande kommandon:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurera IoT hub

Du kan nu konfigurera IoT hub till [ladda upp filer till IoT hub](iot-hub-devguide-file-upload.md) med information om ditt lagringskonto.

Konfigurationen kräver följande värden:

* **Lagringsbehållare**: En blob-behållare i ett Azure storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Du har hämtat den nödvändiga lagringskontoinformation i föregående avsnitt. IoT Hub genererar automatiskt SAS URI: er med skrivbehörighet till den här blob-behållare för enheter som ska användas när de laddar upp filer.

* **Ta emot meddelanden om uppladdade filer**: Aktivera eller inaktivera meddelanden för uppladdning av filen.

* **SAS TTL**: Den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT Hub. Ange en timme som standard.

* **Filen notification inställningar standard TTL**: De time-to-live för en fil ladda upp meddelandet innan det förfaller. Ange en dag som standard.

* **Filen notification maximalt antal leveranser**: Hur många gånger som IoT-hubben försöker leverera en fil att överföra meddelanden. Värdet 10 som standard.

Använd följande PowerShell-cmdlet för att konfigurera filen överföra inställningar på din IoT-hubb:

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

Läs mer om funktionerna du kan ladda upp filen för IoT Hub, [ladda upp filer från en enhet](iot-hub-devguide-file-upload.md).

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub metrics](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Skydda din IoT-lösning från grunden upp](../iot-fundamentals/iot-security-ground-up.md)
