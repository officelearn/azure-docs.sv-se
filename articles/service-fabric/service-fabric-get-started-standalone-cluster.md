---
title: "Konfigurera ett fristående Azure Service Fabric-kluster | Microsoft Docs"
description: "Skapa ett fristående kluster för utveckling med tre noder som körs på samma dator. När du har slutfört den här konfigurationen kan du skapa ett kluster med flera datorer."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 5e32f1e534057b5e8e0ed6d5c0a4631f9fefbca5
ms.lasthandoff: 04/17/2017


---

# <a name="create-your-first-service-fabric-standalone-cluster"></a>Skapa ditt första fristående Service Fabric-kluster
Du kan skapa ett fristående Service Fabric-kluster på valfria virtuella datorer eller datorer som kör Windows Server 2012 R2 eller Windows Server 2016, lokalt eller i molnet. Den här snabbstarten beskriver hur du skapar ett fristående kluster för utveckling på bara några minuter.  När du är klar har du ett kluster med tre noder som körs på en enskild dator som du kan distribuera appar till.

## <a name="before-you-begin"></a>Innan du börjar
Fabric Service tillhandahåller ett konfigurationspaket för att skapa fristående Service Fabric-kluster.  [Ladda ned konfigurationspaketet](http://go.microsoft.com/fwlink/?LinkId=730690).  Packa upp installationspaketet i en mapp på datorn eller den virtuella datorn där du installerar utvecklingsklustret.  Innehållet i konfigurationspaketet beskrivs i detalj [här](service-fabric-cluster-standalone-package-contents.md).

Klusteradministratören som distribuerar och konfigurerar klustret måste ha administratörsbehörighet på datorn. Du kan inte installera Service Fabric på en domänkontrollant.

## <a name="validate-the-environment"></a>Validera miljön
Skriptet *TestConfiguration.ps1* i det fristående paketet används för att analysera bästa praxis och för att kontrollera om ett kluster kan distribueras i en viss miljö. [Distributionsförberedelser](service-fabric-cluster-standalone-deployment-preparation.md) visar krav och förutsättningar. Kör skriptet för att kontrollera om du kan skapa utvecklingsklustret:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Skapa klustret
Flera exempelkonfigurationsfiler för kluster installeras med konfigurationspaketet. *ClusterConfig.Unsecure.DevCluster.json* är den enklaste klusterkonfigurationen: ett oskyddat kluster med tre noder som körs på en enskild dator.  Andra konfigurationsfiler beskriver kluster med en eller flera datorer som skyddas med X.509-certifikat eller Windows-säkerhet.  Du behöver inte ändra några av standardinställningarna i den här självstudien, men titta igenom konfigurationsfilen så att du bekantar dig med inställningarna.  I avsnittet **Noder** beskrivs de tre noderna i klustret: namn, IP-adress, [nodtyp, feldomän och uppgraderingsdomän](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  I avsnittet **Egenskaper** definieras [säkerhet, tillförlitlighetsnivå, diagnostiksamling och nodtyper](service-fabric-cluster-manifest.md#cluster-properties) för klustret.

Det här klustret är inte säkert.  Vem som helst kan ansluta anonymt och utföra hanteringsåtgärder, så produktionskluster bör alltid skyddas med X.509-certifikat eller Windows-säkerhet.  Säkerhetsfunktioner konfigureras bara när kluster skapas, och du kan inte aktivera någon säkerhet i efterhand.  Mer information om klustersäkerheten med Service Fabric finns i avsnittet om hur du [skyddar ett kluster](service-fabric-cluster-security.md).  

Skapa utvecklingsklustret med tre noder genom att köra skriptet *CreateServiceFabricCluster.ps1* från en PowerShell-administratörssession:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Service Fabric-körningspaketet laddas ned automatiskt och installeras när klustret skapas.

## <a name="connect-to-the-cluster"></a>Anslut till klustret
Utvecklingsklustret med tre noder körs nu. ServiceFabric PowerShell-modulen installeras med runtime.  Du kan kontrollera att klustret körs från samma dator eller från en fjärrdator med Service Fabrics runtime.  Cmdleten [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) upprättar en anslutning till klustret.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Fler exempel på hur du ansluter till ett kluster finns i [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md). När du har anslutit till klustret använder du cmdleten [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) för att visa en lista över noder i klustret och statusinformation för varje nod. **HealthState** bör vara *OK* för varje nod.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.5.216.0   0                     Up 03:00:07   00:00:00              Ok
                     vm1      localhost       NodeType1 5.5.216.0   0                     Up 03:00:02   00:00:00              Ok
                     vm0      localhost       NodeType0 5.5.216.0   0                     Up 03:00:01   00:00:00              Ok
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualisera klustret med hjälp av Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett bra verktyg för att visualisera klustret och hantera program.  Service Fabric Explorer är en tjänst som körs i klustret, som du kommer åt med en webbläsare genom att navigera till [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

Instrumentpanelen för klustret innehåller en översikt över klustret, inklusive en sammanfattning av program- och nodhälsan. Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Ta bort klustret
Om du vill ta bort ett kluster kör du PowerShell-skriptet *RemoveServiceFabricCluster.ps1* från paketmappen och lägger till sökvägen till JSON-konfigurationsfilen. Om du vill kan du ange en plats för loggen över borttagningen.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Om du vill ta bort Service Fabrics runtime från datorn kör du följande PowerShell-skript från paketmappen.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat ett fristående utvecklingskluster kan du läsa följande artiklar:
* [Konfigurera ett fristående kluster med flera datorer](service-fabric-cluster-creation-for-windows-server.md) och aktivera säkerhet.
* [Distribuera appar med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
