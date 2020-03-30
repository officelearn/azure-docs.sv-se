---
title: Felsöka Windows-containrar med Service Fabric och VS
description: Lär dig hur du felsöker Windows-behållare i Azure Service Fabric med Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127630"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Så här felar du Windows-behållare i Azure Service Fabric med Visual Studio 2019

Med Visual Studio 2019 kan du felsöka .NET-program i behållare som Service Fabric-tjänster. Den här artikeln visar hur du konfigurerar din miljö och sedan felsöka ett .NET-program i en behållare som körs i ett lokalt Service Fabric-kluster.

## <a name="prerequisites"></a>Krav

* På Windows 10 följer du den här snabbstarten för att [konfigurera Windows 10 för att köra Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* På Windows Server 2016 följer du den här snabbstarten för att [konfigurera Windows 2016 för att köra Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Konfigurera din lokala service fabric-miljö genom att följa [Förbered din utvecklingsmiljö i Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurera utvecklarmiljön för att felsöka behållare

1. Kontrollera att Tjänsten Docker for Window körs innan du fortsätter med nästa steg.

1. Om du vill stödja DNS-matchning mellan behållare måste du konfigurera ditt lokala utvecklingskluster med hjälp av datornamnet. Dessa steg är också nödvändiga om du vill adressera tjänster via omvänd proxy.
   1. Öppna PowerShell som administratör
   2. Navigera till installationsmappen för SDK-kluster, vanligtvis `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Kör skriptet`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Du kan `-CreateOneNodeCluster` använda för att konfigurera ett ennodskluster. Standard kommer att skapa en lokal femnod kluster.
      >

      Mer information om DNS-tjänsten i Service Fabric finns [i DNS-tjänsten i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Mer information om hur du använder Reverse proxy för Service Fabric från tjänster som körs i en behållare finns i [Omvänd proxyspecialhantering för tjänster som körs i behållare](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Kända begränsningar vid felsökning av behållare i Service Fabric

Nedan finns en lista över kända begränsningar med felsökningsbehållare i Service Fabric och möjliga upplösningar:

* Att använda localhost för ClusterFQDNorIP stöder inte DNS-upplösning i behållare.
    * Lösning: Konfigurera det lokala klustret med datornamn (se ovan)
* Om du kör Windows10 i en virtuell dator får du inte tillbaka DNS-svar till behållaren.
    * Lösning: Inaktivera UDP-kontrollsumma avlastning för IPv4 på NIC-nätverkskortet för virtuella datorer
    * Om du kör Windows10 försämras nätverksprestandan på datorn.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Att lösa tjänster i samma program med DNS-tjänstnamnet fungerar inte på Windows10, om programmet har distribuerats med Docker Compose
    * Lösning: Använd servicename.applicationname för att lösa tjänstslutpunkter
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Om du använder IP-adress för ClusterFQDNorIP, kommer ändra primära IP på värden bryta DNS-funktionalitet.
    * Lösning: Återskapa klustret med den nya primära IP-adressen på värden eller använd datornamn. Detta brott är avsiktligt.
* Om FQDN som klustret skapades med inte går att lösa i nätverket misslyckas DNS.
    * Lösning: Återskapa det lokala klustret med hjälp av den primära IP-adressen för värden. Detta misslyckande är avsiktligt.
* När du felsöker en behållare är dockerloggar endast tillgängliga i utdatafönstret i Visual Studio, inte via Api:er för service fabric, inklusive Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Felsöka ett .NET-program som körs i dockerbehållare på Service Fabric

1. Kör Visual Studio som administratör.

1. Öppna ett befintligt .NET-program eller skapa ett nytt.

1. Högerklicka på projektet och välj **Stöd för Lägg till -> Behållare Orchestrator -> Service Fabric**

1. Tryck på **F5** för att börja felsöka programmet.

    Visual Studio stöder konsol- och ASP.NET projekttyper för .NET och .NET Core.

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna i Service Fabric och behållare finns i [översikt över Service Fabric-behållare](service-fabric-containers-overview.md).
