---
title: Ansluta till och hantera Microsoft Azure Data Box Edge-enhet via Windows PowerShell-gränssnittet | Microsoft-dokument
description: I artikeln beskrivs hur du ansluter till och hanterar sedan Data Box Edge via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265485"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Hantera en Azure Data Box Edge-enhet via Windows PowerShell

Med Azure Data Box Edge-lösningen kan du bearbeta data och skicka dem via nätverket till Azure. I den här artikeln beskrivs några av konfigurations- och hanteringsuppgifterna för databoxens edge-enhet. Du kan använda Azure-portalen, det lokala webbgränssnittet eller Windows PowerShell-gränssnittet för att hantera din enhet.

Den här artikeln fokuserar på de uppgifter du utför med PowerShell-gränssnittet.

Den här artikeln innehåller följande procedurer:

- Ansluta till PowerShell-gränssnittet
- Skapa ett supportpaket
- Överför certifikat
- Återställa enheten
- Visa enhetsinformation
- Hämta beräkningsloggar
- Övervaka och felsöka beräkningsmoduler

## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett supportpaket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Överför certifikat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Du kan också ladda upp IoT Edge-certifikat för att aktivera en säker anslutning mellan din IoT Edge-enhet och de nedströmsenheter som kan ansluta till den. Det finns tre IoT Edge-certifikat *(.pem-format)* som du behöver installera:

- Rotcertifikatutfärdarcertifikat eller ägarcertifikatutfärdaren
- Certifikat för enhetscertifikatutfärdarcertifikat
- Certifikat för enhetsnyckel

I följande exempel visas användningen av den här cmdleten för att installera IoT Edge-certifikat:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
När du kör den här cmdleten uppmanas du att ange lösenordet för nätverksresursen.

Mer information om certifikat finns i [Azure IoT Edge-certifikat](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) eller [Installera certifikat på en gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Visa enhetsinformation
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Återställa din enhet

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Hämta beräkningsloggar

Om beräkningsrollen är konfigurerad på enheten kan du också hämta beräkningsloggarna via PowerShell-gränssnittet.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-AzureDataBoxEdgeComputeRoleLogs` för att hämta beräkningsloggarna för din enhet.

    I följande exempel visas användningen av den här cmdleten:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Här är en beskrivning av de parametrar som används för cmdlet:
    - `Path`: Ange en nätverkssökväg till resursen där du vill skapa beräkningsloggpaketet.
    - `Credential`: Ange användarnamnet för nätverksresursen. När du kör den här cmdleten måste du ange delningslösenordet.
    - `FullLogCollection`: Den här parametern säkerställer att loggpaketet innehåller alla beräkningsloggar. Som standard innehåller loggpaketet bara en delmängd av loggar.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Övervaka och felsöka beräkningsmoduler

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Avsluta fjärrsessionen

Om du vill avsluta fjärr-PowerShell-sessionen stänger du PowerShell-fönstret.

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Data Box Edge](data-box-edge-deploy-prep.md) på Azure-portalen.
