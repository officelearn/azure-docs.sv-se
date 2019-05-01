---
title: Ansluta till och hantera Microsoft Azure Data Box Edge-enhet via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och hantera sedan Data Box Edge via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 8cd89b21e80662ec50746e0c7721a5544cfbce30
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717497"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Hantera en Azure Data Box Edge-enhet via Windows PowerShell

Azure Data Box Edge-lösning kan du bearbeta data och skicka den via nätverket till Azure. Den här artikeln beskriver några av konfiguration och hanteringsaktiviteter för din Data Box Edge-enhet. Du kan använda Azure-portalen, lokala webbgränssnittet eller Windows PowerShell-gränssnittet för att hantera din enhet.

Den här artikeln handlar om de uppgifter som du gör med hjälp av PowerShell-gränssnittet.

Den här artikeln innehåller följande procedurer:

- Ansluta till PowerShell-gränssnittet
- Skapa ett supportpaket
- Överför certifikat
- Återställa enheten
- Visa enhetsinformation
- Hämta loggar för beräkning
- Övervaka och felsöka beräkning moduler

## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett supportpaket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Överför certifikat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Du kan också ladda upp IoT Edge-certifikat för att aktivera en säker anslutning mellan din IoT Edge-enhet och de underordnade enheter som kan ansluta till den. Det finns tre IoT Edge-certifikat (*.pem* format) som du behöver installera:

- Rotcertifikatutfärdarcertifikat eller om ägarens CA
- Enhets-CA-certifikat
- Enheten nyckelcertifikat

I följande exempel visas användningen av denna cmdlet för att installera IoT Edge-certifikat:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Mer information om certifikat finns i [Azure IoT Edge certifikat](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) eller [installera certifikat på en gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Visa enhetsinformation
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Återställa en enhet

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Hämta loggar för beräkning

Du kan också få beräkning loggarna via PowerShell-gränssnittet om beräkningsrollen har konfigurerats på enheten.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd den `Get-AzureDataBoxEdgeComputeRoleLogs` att hämta loggarna beräkning för din enhet.

    I följande exempel visas användningen av denna cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    Här följer en beskrivning av de parametrar som används för cmdleten:
    - `Path`: Ange en nätverkssökväg till den resurs där du vill skapa beräkning log-paketet.
    - `Credential`: Ange användarnamnet och lösenordet för nätverksresursen.
    - `RoleInstanceName`: Ange den här strängen `IotRole` för den här parametern.
    - `FullLogCollection`: Den här parametern säkerställer att log-paketet innehåller alla loggar för beräkning. Som standard innehåller log-paketet endast en delmängd av loggar.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Övervaka och felsöka beräkning moduler

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Avsluta fjärrsessionen

Stäng fönstret PowerShell om du vill avsluta fjärrsession i PowerShell.

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Data Box Edge](data-box-edge-deploy-prep.md) på Azure-portalen.
