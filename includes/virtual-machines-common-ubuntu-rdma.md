---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67671210"
---
1. Installera dapl, rdmacm, ibverbs och mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. I /etc/waagent.conf aktiverar du RDMA genom att ta av följande konfigurationsrader. Du behöver root-åtkomst för att redigera den här filen.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Lägg till eller ändra följande minnesinställningar i KB i filen /etc/security/limits.conf. Du behöver root-åtkomst för att redigera den här filen. För teständamål kan du ställa memlock till obegränsad. Till exempel: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Installera Intel MPI-bibliotek. Antingen [köpa och ladda ner](https://software.intel.com/intel-mpi-library/) biblioteket från Intel eller ladda ner gratis utvärdering [version](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Endast Intel MPI 5.x runtimes stöds.
 
   Installationssteg finns i [installationsguiden för Intel MPI-bibliotek](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Aktivera ptrace för icke-rota icke-felsökningsprocesser (behövs för de senaste versionerna av Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
