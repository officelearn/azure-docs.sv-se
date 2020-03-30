---
title: Installation av AMD GPU-drivrutin i Azure N-serien för Windows
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269425"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installera AMD GPU-drivrutiner på virtuella datorer i N-serien som kör Windows

För att kunna dra nytta av GPU-funktionerna i de nya virtuella datorerna i Azure NVv4-serien som kör Windows måste AMD GPU-drivrutiner installeras. AMD-drivrutinstillägget kommer att finnas tillgänglig under de kommande veckorna. Den här artikeln innehåller operativsystem, drivrutiner och steg för manuell installation och verifiering som stöds.

Grundläggande specifikationer, lagringskapacitet och diskinformation finns i [GPU Windows VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows 10 EVD - Bygg 1903 <br/><br/>Windows 10 - Bygg 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Installation av drivrutiner

1. Anslut med fjärrskrivbord till varje virtuell dator i NVv4-serien.

2. Om du är en NVv4 förhandsgranska kund sedan stoppa den virtuella datorn och vänta tills den ska flytta till Stoppad (Deallocated) tillstånd.

3. Starta den virtuella datorn och avinstallera sedan förhandsgranskningsdrivrutinen genom att köra "amdcleanuputility-x64.exe" som finns i mappen "...\AMDCleanUninstallUtility". Den exakta sökvägen varierar beroende på var de tidigare installationsfilerna för drivrutiner finns.  

4. Ladda ner och installera den senaste drivrutinen.

5. Starta om den virtuella datorn.

## <a name="verify-driver-installation"></a>Verifiera installationen av drivrutiner

Du kan verifiera drivrutinsinstallationen i Enhetshanteraren. The following example shows successful configuration of the Radeon Instinct MI25 card on an Azure NVv4 VM.
<br />
![Egenskaper för GPU-drivrutin](./media/n-series-amd-driver-setup/device-manager.png)

Du kan använda dxdiag för att verifiera GPU-visningsegenskaperna, inklusive video-RAM-minnet. Följande exempel visar en 1/8:e partition av Radeon Instinct MI25-kortet på en Azure NVv4-virtuell dator.
<br />
![Egenskaper för GPU-drivrutin](./media/n-series-amd-driver-setup/dxdiag.png)
