---
title: Konfiguration av Azure N-seriens AMD GPU-drivrutin för Windows
description: Konfigurera AMD GPU-drivrutiner för virtuella datorer i N-serien som kör Windows Server eller Windows i Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269425"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installera AMD GPU-drivrutiner för virtuella datorer i N-serien som kör Windows

För att kunna dra nytta av GPU-funktionerna i de nya virtuella Azure NVv4-serierna som kör Windows, måste AMD GPU-drivrutinerna vara installerade. Tillägget för AMD-drivrutinen är tillgängligt under de kommande veckorna. Den här artikeln innehåller operativ system, driv rutiner och manuella installations-och verifierings steg.

Grundläggande specifikationer, lagrings kapacitet och disk information finns i storlekar för [GPU Windows VM](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows 10-EVD – build 1903 <br/><br/>Windows 10 – build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Driv rutins installation

1. Anslut via fjärr skrivbord till varje virtuell NVv4-serien.

2. Om du är en NVv4 för hands version stoppar du den virtuella datorn och väntar på att den ska flyttas till stoppat (friallokerat) tillstånd.

3. Starta den virtuella datorn och avinstallera sedan Preview-drivrutinen genom att köra "amdcleanuputility-x64. exe" i mappen ". ..\AMDCleanUninstallUtility". Den exakta sökvägen varierar beroende på var de tidigare installationsfilerna för driv rutinen finns.  

4. Hämta och installera den senaste driv rutinen.

5. Starta om den virtuella datorn.

## <a name="verify-driver-installation"></a>Verifiera installation av driv rutin

Du kan kontrol lera driv rutins installationen i Enhetshanteraren. I följande exempel visas lyckad konfiguration av kortet Radeon Instinct MI25 på en virtuell Azure-NVv4.
<br />
egenskaper för ![GPU-drivrutin](./media/n-series-amd-driver-setup/device-manager.png)

Du kan använda dxdiag för att kontrol lera GPU-bildskärms egenskaper inklusive video-RAM. I följande exempel visas en 1/8-partition av kortet Radeon Instinct MI25 på en virtuell Azure-NVv4.
<br />
egenskaper för ![GPU-drivrutin](./media/n-series-amd-driver-setup/dxdiag.png)
