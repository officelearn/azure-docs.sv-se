---
title: Använd Azure PowerShell för att konfigurera ladda upp filen | Microsoft Docs
description: Hur du använder Azure PowerShell-cmdlets för att konfigurera din IoT-hubb för att aktivera filen filöverföringar från anslutna enheter. Innehåller information om hur du konfigurerar målet Azure storage-konto.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 22d331342504aa7cc3d4fe86777ef9c2595a27ac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurera IoT-hubb filöverföringar med hjälp av PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Att använda den [filen överför funktioner i IoT-hubb][lnk-upload], måste du först associera ett Azure storage-konto med din IoT-hubb. Du kan använda ett befintligt lagringskonto eller skapa en ny.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure PowerShell-cmdlets][lnk-powershell-install].
* Azure IoT-hubb. Om du inte har en IoT-hubb kan du använda den [cmdlet New-AzureRmIoTHub] [ lnk-powershell-iothub] att skapa något eller använder portalen för att [skapar en IoT-hubb][lnk-portal-hub].
* Ett Azure-lagringskonto. Om du inte har ett Azure storage-konto kan du använda den [Azure Storage PowerShell-cmdlets] [ lnk-powershell-storage] att skapa något eller använder portalen för att [skapa ett lagringskonto][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välj din prenumeration.

1. I PowerShell-Kommandotolken kör den **Connect-AzureRmAccount** cmdlet:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Om du har flera Azure-prenumerationer, ger logga in på Azure åtkomst till alla de Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

    ```powershell
    Get-AzureRMSubscription
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandon för att hantera din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Hämta information om ditt lagringskonto

Följande steg förutsätter att du har skapat din storage-konto med den **Resource Manager** distributionsmodellen, och inte den **klassiska** distributionsmodell.

Om du vill konfigurera filöverföringar från dina enheter, behöver du anslutningssträngen för Azure storage-konto. Lagringskontot måste vara i samma prenumeration som din IoT-hubb. Du måste också namnet på en blob-behållare i storage-konto. Använd följande kommando för att hämta dina nycklar för lagringskonto:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anteckna den **key1** nyckelvärdet för storage-konto. Du behöver den i följande steg.

Du kan använda en befintlig blobbehållare för dina filöverföringar eller skapa nya:

* Om du vill visa en lista över de befintliga blob-behållarna i ditt lagringskonto, använder du följande kommandon:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Använd följande kommandon för att skapa en blob-behållare på ditt lagringskonto:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurera din IoT-hubb

Du kan nu konfigurera din IoT-hubb för att aktivera [filen överför funktioner] [ lnk-upload] med information om ditt lagringskonto.

Konfigurationen kräver följande värden:

**Lagringsbehållaren**: en blob-behållare i ett Azure storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Du har hämtat informationen om behövs lagring i föregående avsnitt. IoT-hubb genererar automatiskt SAS URI: er med behörighet att skriva till blob-behållare för enheter som ska användas när de överför filer.

**Ta emot meddelanden för överförda filer**: aktivera eller inaktivera filen överför meddelanden.

**SAS TTL**: den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT-hubb. Ange till en timme som standard.

**Filen notification inställningar Standardbegränsning**: den time-to-live av en fil överför meddelande innan det förfaller. Ange en dag som standard.

**Meddelande leverans av maximalt antal filer**: antalet gånger som IoT-hubben försöker att leverera en fil överför meddelande. Satt till 10 som standard.

Använd följande PowerShell-cmdlet för att konfigurera filen överför inställningar på din IoT-hubb:

```powershell
Set-AzureRmIotHub `
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

Mer information om funktionerna som överför filen IoT-hubb finns [överföra filer från en enhet][lnk-upload].

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [Massinläsning hantera IoT-enheter][lnk-bulk]
* [IoT-hubb mått][lnk-metrics]
* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]
* [Skydda din IoT-lösning från grunden upp][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
