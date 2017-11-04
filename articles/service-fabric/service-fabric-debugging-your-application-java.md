---
title: "Felsöka Azure Service Fabric programmet i Eclipse | Microsoft Docs"
description: "Förbättra tillförlitligheten och prestandan hos dina tjänster genom att utveckla och felsökning av dem i Eclipse i ett kluster för lokal utveckling."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: f3bcee3794de35005bd387ecfae7e6707f3cb5ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Felsöka med Eclipse programmet Java Service Fabric
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Starta en lokal utveckling klustret genom att följa stegen i [ställa in din utvecklingsmiljö för Service Fabric](service-fabric-get-started-linux.md).

2. Uppdatera entryPoint.sh för tjänsten som du vill felsöka, så att den startar java-processen med fjärråtkomst debug-parametrar. Den här filen finns på följande plats: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. Port 8001 har angetts för felsökning i det här exemplet.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Uppdatera applikationsmanifestet genom att ange instansantalet eller replikantalet för tjänsten som felsöks är 1. Den här inställningen förhindrar konflikter för den port som används för felsökning. Ange till exempel för tillståndslösa tjänster ``InstanceCount="1"`` och för tillståndskänsliga tjänster ange målet och min replikuppsättningen storlekar till 1 på följande sätt: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Distribuera programmet.

5. I Eclipse IDE, väljer **kör -> Felsöka konfigurationer > Remote Java-program och ange anslutningsegenskaper** och ange egenskaperna på följande sätt:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ange brytpunkter på önskade distributionsplatser och felsöka programmet.

Om programmet kraschar, kan du också vill aktivera coredumps. Köra ``ulimit -c`` i ett gränssnitt och om den returnerar 0 kommer coredumps inte har aktiverats. Om du vill aktivera obegränsade coredumps, kör du följande kommando: ``ulimit -c unlimited``. Du kan också kontrollera status med hjälp av kommandot ``ulimit -a``.  Om du vill uppdatera coredump generation sökvägen köra ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Nästa steg

* [Samla in loggar med hjälp av Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md).
* [Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
