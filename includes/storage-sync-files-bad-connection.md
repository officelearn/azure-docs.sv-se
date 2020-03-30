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
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75772887"
---
Det här felet kan inträffa när Azure File Sync-tjänsten inte kan nås från servern. Du kan felsöka det här felet genom att utföra följande steg:

1. Kontrollera att `FileSyncSvc.exe` Windows-tjänsten inte blockeras av brandväggen.
2. Kontrollera att port 443 är öppen för utgående anslutningar till Azure File Sync-tjänsten. Du kan göra `Test-NetConnection` detta med cmdlet. Du hittar webbadressen för platshållaren `<azure-file-sync-endpoint>` nedan i dokumentet [Proxy- och brandväggsinställningar för Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Se till att proxykonfigurationen är inställd som förväntat. Det kan du göra med cmdleten `Get-StorageSyncProxyConfiguration`. Mer information om konfiguration av proxykonfigurationen för Azure File Sync finns i [Proxy- och brandväggsinställningar för Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Använd cmdleten Test-StorageSyncNetworkConnectivity för att kontrollera nätverksanslutningen till tjänstslutpunkterna. Mer information finns i [Testa nätverksanslutning till tjänstslutpunkter](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Kontakta nätverksadministratören om du vill ha ytterligare hjälp med felsökning av nätverksanslutningen.
