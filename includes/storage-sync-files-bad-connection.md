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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75772887"
---
Det här felet kan inträffa när Azure File Sync-tjänsten inte kan nås från servern. Du kan felsöka det här felet genom att utföra följande steg:

1. Kontrol lera att Windows `FileSyncSvc.exe` -tjänsten inte blockeras av brand väggen.
2. Kontrol lera att port 443 är öppen för utgående anslutningar till tjänsten Azure File Sync. Du kan göra detta med `Test-NetConnection` cmdleten. Du hittar webbadressen för platshållaren `<azure-file-sync-endpoint>` nedan i dokumentet [Proxy- och brandväggsinställningar för Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Se till att proxykonfigurationen är inställd som förväntat. Det kan du göra med cmdleten `Get-StorageSyncProxyConfiguration`. Mer information om konfiguration av proxykonfigurationen för Azure File Sync finns i [Proxy- och brandväggsinställningar för Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Använd cmdleten test-StorageSyncNetworkConnectivity för att kontrol lera nätverks anslutningen till tjänstens slut punkter. Läs mer i [testa nätverks anslutningen till tjänstens slut punkter](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Kontakta nätverks administratören om du vill ha mer information om fel sökning av nätverks anslutningar.
