---
title: Felsöka ditt program i Sol förmörkelse
description: Förbättra tillförlitligheten och prestandan hos dina tjänster genom att utveckla och felsöka dem i Sol förmörkelse i ett lokalt utvecklings kluster.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614493"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Felsöka Java Service Fabric program med hjälp av Sol förmörkelse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Sol förmörkelse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Starta ett lokalt utvecklings kluster genom att följa stegen i Konfigurera [din Service Fabric utvecklings miljö](service-fabric-get-started-linux.md).

2. Uppdatera entryPoint.sh för den tjänst som du vill felsöka, så att den startar Java-processen med fjärrfelsöknings parametrar. Du hittar den här filen på följande plats: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. Port 8001 har angetts för felsökning i det här exemplet.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Uppdatera applikations manifestet genom att ange antalet instanser eller replik antalet för tjänsten som ska felsökas till 1. Den här inställningen förhindrar konflikter kring den port som används för felsökning. För tillståndslösa tjänster kan du t.ex. ange `InstanceCount="1"` och för tillståndskänsliga tjänster kan du ange målet och minsta replikuppsättningsstorlek till 1 enligt följande: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Distribuera programmet.

5. I de Sol förmörkelse-IDE: en väljer du **kör > felsöknings konfiguration – > fjärr-Java-program och egenskaper för ingående anslutning** och anger egenskaperna enligt följande:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ange Bryt punkter i önskade punkter och Felsök programmet.

Om programmet kraschar kanske du också vill aktivera coredumps. Kör `ulimit -c` i ett gränssnitt och om det returnerar 0 aktive ras inte coredumps. Om du vill aktivera obegränsade coredumps kör du följande `ulimit -c unlimited`kommando:. Du kan också kontrol lera statusen med hjälp av `ulimit -a`kommandot.  Om du vill uppdatera coredump generations Sök väg kör `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`du. 

### <a name="next-steps"></a>Nästa steg

* [Samla in loggar med Linux Azure-diagnostik](service-fabric-diagnostics-how-to-setup-lad.md).
* [Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
