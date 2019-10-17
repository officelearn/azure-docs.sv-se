---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391803"
---
Det här felet kan inträffa när Azure File Sync tjänsten inte kan nås från servern. Du kan felsöka det här felet genom att gå igenom följande steg:

1. Kontrol lera att Windows-tjänsten `FileSyncSvc.exe` inte blockeras av brand väggen.
2. Kontrol lera att port 443 är öppen för utgående anslutningar till tjänsten Azure File Sync. Du kan göra detta med cmdleten `Test-NetConnection`. URL: en för plats hållaren `<azure-file-sync-endpoint>` nedan kan hittas i dokumentet [Azure File Sync proxy och brand Väggs inställningar](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) . 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Se till att proxykonfigurationen anges som förväntat. Detta kan göras med cmdleten `Get-StorageSyncProxyConfiguration`. Mer information om konfiguration av proxykonfigurationen för Azure File Sync finns i [Azure File Sync proxy-och brand Väggs inställningar](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Kontakta nätverks administratören om du vill ha mer information om fel sökning av nätverks anslutningar.