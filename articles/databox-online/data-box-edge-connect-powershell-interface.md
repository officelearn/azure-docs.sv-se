---
title: Ansluta till och hantera Microsoft Azure Data Box Edge-enhet via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och hantera sedan Data Box Edge via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 9b0e94deda205497cda4ebf383f302c6c3bb896a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403603"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Hantera en Azure Data Box Edge-enhet via Windows PowerShell

Azure Data Box Edge-lösning kan du bearbeta data och skicka den via nätverket till Azure. Den här artikeln beskriver några av konfiguration och hanteringsaktiviteter för din Data Box Edge-enhet. Du kan använda Azure-portalen, lokala webbgränssnittet eller Windows PowerShell-gränssnittet för att hantera din enhet.

Den här artikeln handlar om de uppgifter som du gör med hjälp av PowerShell-gränssnittet.

Den här artikeln innehåller följande procedurer:

- Ansluta till PowerShell-gränssnittet
- Starta en supportsession
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

## <a name="view-device-information"></a>Visa enhetsinformation
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Återställa en enhet

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Hämta loggar för beräkning

Du kan också få beräkning loggarna via PowerShell-gränssnittet om beräkningsrollen har konfigurerats på enheten.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd den `Get-AzureDataBoxEdgeComputeRoleLogs` att hämta loggarna beräkning för din enhet.

    I följande exempel visas användningen av denna cmdlet:

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Här följer en beskrivning av de parametrar som används för cmdleten:
    - `Path`: Ange en nätverkssökväg till den resurs där du vill skapa beräkning log-paketet.
    - `Credential`: Ange användarnamnet och lösenordet för nätverksresursen.
    - `RoleInstanceName`: Ange den här strängen `IotRole` för den här parametern.
    - `FullLogCollection`: Den här parametern säkerställer att log-paketet innehåller alla loggar för beräkning. Som standard innehåller log-paketet endast en delmängd av loggar.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Övervaka och felsöka beräkning moduler

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Data Box Edge](data-box-edge-deploy-prep.md) på Azure-portalen.
