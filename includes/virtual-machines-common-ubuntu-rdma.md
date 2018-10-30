---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 7f5b829399004a58ba84dc9abc34b2c9dd544fbf
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227267"
---
1. Installera dapl, rdmacm, ibverbs och mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. Aktivera RDMA på/etc/waagent.conf, av uncommenting följande konfigurationsrader. Du behöver rotåtkomst att redigera den här filen.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Lägg till eller ändra följande minnesinställningar i KB i filen /etc/security/limits.conf. Du behöver rotåtkomst att redigera den här filen. I testsyfte kan du ange memlock till obegränsad. Till exempel: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Installera Intel MPI-biblioteket. Antingen [köpa och ladda ned](https://software.intel.com/intel-mpi-library/) biblioteket från Intel eller ladda ned den [gratisversion](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
  wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
  ```
 
 Endast Intel MPI 5.x körningar stöds.
 
 Anvisningar för installation finns i den [installationsguide för Intel MPI biblioteket](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Aktivera ptrace för icke-felsökare processer (som behövs för de senaste versionerna av Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
