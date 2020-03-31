---
title: Felsöka ditt program i Eclipse
description: Förbättra tillförlitligheten och prestandan hos dina tjänster genom att utveckla och felsöka dem i Eclipse i ett lokalt utvecklingskluster.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614493"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Felsöka java-tjänst fabric-programmet med Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/ Java](service-fabric-debugging-your-application-java.md)
> 

1. Starta ett lokalt utvecklingskluster genom att följa stegen i [Konfigurera utvecklingsmiljön för serviceinfrastruktur](service-fabric-get-started-linux.md).

2. Uppdatera entryPoint.sh av den tjänst du vill felsöka, så att den startar java-processen med fjärrdebugparametrar. Den här filen finns på `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`följande plats: . Port 8001 har angetts för felsökning i det här exemplet.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Uppdatera programmanifestet genom att ange instansantalet eller antalet repliker för tjänsten som används för att inaktivera 1. Den här inställningen förhindrar konflikter kring den port som används för felsökning. För tillståndslösa tjänster kan du t.ex. ange `InstanceCount="1"` och för tillståndskänsliga tjänster kan du ange målet och minsta replikuppsättningsstorlek till 1 enligt följande: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Distribuera programmet.

5. I Eclipse IDE väljer du **Kör -> Felsökningskonfigurationer -> egenskaper för fjärr-Java-program och egenskaper för indataanslutning** och anger egenskaperna för egenskaper enligt följande:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ange brytpunkter vid önskade punkter och felsöka programmet.

Om programmet kraschar kanske du också vill aktivera coredumps. Kör `ulimit -c` i ett skal och om det returnerar 0 aktiveras inte kärndumpar. Om du vill aktivera obegränsade kärndumpar kör du följande kommando: `ulimit -c unlimited`. Du kan också verifiera status `ulimit -a`med kommandot .  Om du vill uppdatera genereringsvägen för `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`kärndump kör du . 

### <a name="next-steps"></a>Nästa steg

* [Samla in loggar med Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md).
* [Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
