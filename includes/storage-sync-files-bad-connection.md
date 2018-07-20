---
title: ta med fil
description: ta med fil
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 754562487f0fe9f825107445a3059cc15a1faa26
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39146264"
---
Det här felet kan inträffa när Azure File Sync-tjänsten inte kan nås från servern. Du kan felsöka det här felet genom att utföra följande steg:

1. Kontrollerar att Windows-tjänsten `FileSyncSvc.exe` inte har blockerats av brandväggen.
2. Kontrollera att port 443 är öppen för utgående anslutningar till Azure File Sync-tjänsten. Du kan göra detta med den `Test-NetConnection` cmdlet. URL-Adressen för den `<azure-file-sync-endpoint>` platshållaren nedan hittar du i den [Azure File Sync-inställningar för proxy och brandvägg](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) dokumentet. 

    ```PowerShell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Kontrollera att proxykonfigurationen är inställd som förväntat. Detta kan göras med den `Get-StorageSyncProxyConfiguration` cmdlet. Mer information om hur du konfigurerar proxykonfigurationen för Azure File Sync finns i den [Azure File Sync-inställningar för proxy och brandvägg](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```PowerShell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Kontakta nätverksadministratören för ytterligare hjälp med felsökning av nätverksanslutningen.