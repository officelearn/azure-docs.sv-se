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
ms.openlocfilehash: 7527cd59ef812c108cc53f84ad19f4d2cff9ec5e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893570"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installera AMD GPU-drivrutiner för virtuella datorer i N-serien som kör Windows

För att kunna dra nytta av GPU-funktionerna i de nya virtuella Azure NVv4-serierna som kör Windows, måste AMD GPU-drivrutinerna vara installerade. Tillägget för AMD-drivrutinen är tillgängligt under de kommande veckorna. Den här artikeln innehåller operativ system, driv rutiner och manuella installations-och verifierings steg.

Grundläggande specifikationer, lagrings kapacitet och disk information finns i storlekar för [GPU Windows VM](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

| OS | Drivrutin |
| -------- |------------- |
| Windows 10-EVD – build 1903 <br/><br/>Windows 10 – build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (. exe) |

## <a name="driver-installation"></a>Driv rutins installation

1. Anslut via fjärr skrivbord till varje virtuell NVv4-serien.

1. Hämtningsstatus och Extrahera installationsfilerna för driv rutinen. Navigera till mappen och kör setup. exe för att installera driv rutinen som stöds för ditt Windows-operativsystem.

## <a name="verify-driver-installation"></a>Verifiera installation av driv rutin

Du kan kontrol lera driv rutins installationen i Enhetshanteraren. I följande exempel visas lyckad konfiguration av kortet Radeon Instinct MI25 på en virtuell Azure-NVv4.
<br />
egenskaper för ![GPU-drivrutin](./media/n-series-amd-driver-setup/device-manager.png)

Du kan använda dxdiag för att kontrol lera GPU-bildskärms egenskaper inklusive video-RAM. I följande exempel visas en 1/8-partition av kortet Radeon Instinct MI25 på en virtuell Azure-NVv4.
<br />
egenskaper för ![GPU-drivrutin](./media/n-series-amd-driver-setup/dxdiag.png)
