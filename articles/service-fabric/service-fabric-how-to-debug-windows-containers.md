---
title: Felsöka Windows-behållare med Service Fabric och VS | Microsoft Docs
description: Lär dig att felsöka Windows-behållare i Azure Service Fabric med hjälp av Visual Studio 2017.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: bca33fe187668d38d4451b2de5b9e54d86e40ba9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655754"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Så här: felsöka Windows-behållare i Azure Service Fabric med hjälp av Visual Studio 2017

Med Visual Studio 2017 uppdatering 7 (15.7), kan du felsöka .NET-program i behållare som Service Fabric-tjänster. Den här artikeln visar hur du konfigurerar din miljö och felsöka ett .NET-program i en behållare som körs i en lokal Service Fabric-klustret.

## <a name="prerequisites"></a>Förutsättningar

* Windows 10, så den här snabbstartsguide till [konfigurera Windows 10 för att köra Windows behållare](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Följ denna Snabbstart till på Windows Server 2016 [2016 konfigurera Windows för att köra Windows behållare](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Konfigurera din lokala Service Fabric-miljö genom att följa [förbereda din utvecklingsmiljö i Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurera din miljö för att felsöka behållare för utvecklare

1. Se till att Docker för Windows-tjänst körs innan du fortsätter med nästa steg.

1. För att stödja DNS-matchning mellan behållare, behöver du konfigurera klustret för lokal utveckling med hjälp av namnet på datorn.
    1. Öppna PowerShell som administratör
    1. Navigera till mappen för SDK-klustret vanligtvis `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Kör skriptet `DevClusterSetup.ps1` med parametern `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > Du kan använda den `-CreateOneNodeCluster` att konfigurera ett kluster med en nod. Standard skapar ett lokala kluster med fem noder.
    >

    Mer information om DNS-tjänsten i Service Fabric finns [DNS-tjänsten i Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Kända begränsningar vid felsökning av behållare i Service Fabric

Nedan visas en lista över kända begränsningar med felsökning behållare i Service Fabric och möjliga lösningar:

* Med hjälp av localhost för ClusterFQDNorIP stöder inte DNS-matchning i behållare.
    * Lösning: Ställ in det lokala klustret med hjälp av namnet på datorn (se ovan)
* Kör Windows10 på en virtuell dator får inte DNS-svar tillbaka till behållaren.
    * Lösning: Inaktivera avlastning av UDP-kontrollsumma för IPv4 på nätverkskort för virtuella datorer
    * Observera att detta försämrar nätverksprestanda på datorn.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Lösa tjänster i samma program med hjälp av DNS fungerar tjänstnamn inte på Windows10, om programmet distribuerades med Docker Compose
    * Lösning: Använd servicename.applicationname för att matcha slutpunkter
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Om du använder IP-adress för ClusterFQDNorIP, bryts ändra primära IP-adress på värden DNS-funktioner.
    * Lösning: Återskapa klustret med den nya primära IP på värden eller Använd namnet på datorn. Det här är avsiktligt.
* Om klustret har skapats med FQDN inte är matchas i nätverket, misslyckas DNS.
    * Lösning: Återskapa det lokala klustret med hjälp av den primära IP-Adressen för värden. Det här är avsiktligt.
* När du felsöker en behållare kan docker loggar bara användas i utdatafönstret Visual Studio, inte via Service Fabric-API, inklusive Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Felsöka ett .NET-program som körs i docker behållare på Service Fabric

1. Kör Visual Studio som administratör.

1. Öppna ett befintligt .NET-program eller skapa en ny.

1. Högerklicka på projektet och välj **behållare Orchestrator Support -> Lägg till Service Fabric ->**

1. Tryck på **F5** att starta felsökningen av programmet.

    Visual Studio stöder konsolen och ASP.NET projekttyper för .NET och .NET Core.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om funktionerna i Service Fabric och behållare, följ länken: [översikt över Service Fabric-behållare](service-fabric-containers-overview.md).