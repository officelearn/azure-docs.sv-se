---
title: "Skapa ett fristående Azure Service Fabric-kluster | Microsoft Docs"
description: "Skapa ett Azure Service Fabric-kluster på en dator (fysiska eller virtuella) med Windows Server, om den är lokalt eller i något moln."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik;dekapur
ms.openlocfilehash: 6aa2905a97ec6b8c125f2ab9572a8e40bf525b27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Skapa ett fristående kluster som körs på Windows Server
Du kan använda Azure Service Fabric skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server. Det innebär att distribuera och köra Service Fabric-program i en miljö som innehåller en uppsättning sammankopplade Windows Server-datorer, att den lokala eller med en molntjänstleverantör för. Service Fabric ger ett installationspaket för att skapa Service Fabric-kluster kallas det fristående installationspaketet för Windows Server.

Den här artikeln vägleder dig genom stegen för att skapa en fristående Service Fabric-klustret.

> [!NOTE]
> Det här fristående Windows Server-paketet är kommersiellt tillgängliga och kan användas för Produktionsdistribution. Det här paketet kan innehålla nya Service Fabric-funktioner som finns i ”förhandsversion”. Rulla ned till ”[Förhandsgranska funktioner som ingår i det här paketet](#previewfeatures_anchor)”. avsnittet för listan över funktioner för förhandsgranskning. Du kan [hämta en kopia av LICENSVILLKOREN](http://go.microsoft.com/fwlink/?LinkID=733084) nu.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Få support för Service Fabric för Windows Server-paket
* Fråga community om det fristående Service Fabric-paketet för Windows Server i den [Azure Service Fabric-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Skapa ett ärende för [Professional stöd för Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Mer information om Professional Support från Microsoft [här](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Du kan också få stöd för det här paketet som en del av [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Mer information, se [Azure Service Fabric supportalternativ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).
* Om du vill samla in loggar för support, kör den [logginsamlaren för Service Fabric fristående](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Hämta Service Fabric för Windows Server
Använd Service Fabric för Windows Server-paketet för att skapa klustret (Windows Server 2012 R2 och senare) finns här: <br>
[Hämta länk - Service Fabric fristående paket - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Hitta information om innehållet i paketet [här](service-fabric-cluster-standalone-package-contents.md).

Service Fabric runtime-paketet hämtas automatiskt när klustret skapas. Distribuera från en dator som inte är ansluten till internet, hämta runtime-paketet out of band härifrån: <br>
[Hämta länk - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Hitta fristående klusterkonfigurationen exempel på: <br>
[Exempel på fristående klusterkonfigurationen](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Skapa klustret
Service Fabric kan distribueras till ett kluster med en dator utveckling med hjälp av den *ClusterConfig.Unsecure.DevCluster.json* -fil som ingår i [exempel](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Packa upp paketet fristående till din dator, kopiera exempel konfigurationsfilen till den lokala datorn och kör sedan den *CreateServiceFabricCluster.ps1* skript via en administratör PowerShell-session från mappen fristående paketet :
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Steg 1A: skapa ett oskyddat lokal utveckling-kluster
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

I avsnittet Miljökonfiguration på [planera och förbereda distributionen av klustret](service-fabric-cluster-standalone-deployment-preparation.md) för felsökningsinformation.

Om du är klar körs utvecklingsscenarier, du kan ta bort Service Fabric-kluster från datorn genom att referera till stegen i avsnittet ”[tar bort ett kluster](#removecluster_anchor)”. 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Steg 1B: skapa ett kluster för flera datorer
När du har gått igenom planering och förberedelser anges i de nedanstående länk, är du redo att skapa klustret produktion med klustret konfigurationsfilen. <br>
[Planera och förbereda distributionen av kluster](service-fabric-cluster-standalone-deployment-preparation.md)

1. Verifiera den konfigurationsfil som du har skrivit genom att köra den *TestConfiguration.ps1* skript från mappen fristående paketet:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Du bör se utdata som nedan. Om fältet längst ned ”skickades” returneras som ”True” hälsokontroller godkänts och klustret ser ut för att kunna distribueras baserat på konfigurationen av indata.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Skapa klustret: kör den *CreateServiceFabricCluster.ps1* skript för att distribuera Service Fabric-kluster i varje dator i konfigurationen. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Distribution spårningar skrivs till den Virtuella/datorn där du körde CreateServiceFabricCluster.ps1 PowerShell-skript. Dessa finns i undermappen DeploymentTraces, i katalogen där skriptet kördes. Hitta de installerade filerna i katalogen FabricDataRoot enligt anvisningarna i klustret konfigurationsfilen FabricSettings avsnitt (som standard c:\ProgramData\SF) för att se om Service Fabric har distribuerats korrekt till en dator. FabricHost.exe och Fabric.exe processer kan också ses körs i Aktivitetshanteraren.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Steg 1C: skapa ett kluster som offline (internet frånkopplad)
Service Fabric runtime-paketet hämtas automatiskt när klustret skapas. När du distribuerar ett kluster till datorer som inte är ansluten till internet, måste du hämta Service Fabric runtime-paketet separat och ange sökvägen till den när klustret skapas.
Runtime-paketet kan laddas ned separat, från en annan dator som är ansluten till internet, vid [Hämta länk - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Kopiera runtime-paketet till där du distribuerar offline klustret från och skapa klustret genom att köra `CreateServiceFabricCluster.ps1` med den `-FabricRuntimePackagePath` parametern ingår, enligt nedan: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
där `.\ClusterConfig.json` och `.\MicrosoftAzureServiceFabric.cab` är respektive sökvägar till klusterkonfigurationen och runtime CAB-filen.


### <a name="step-2-connect-to-the-cluster"></a>Steg 2: Anslut till klustret
Om du vill ansluta till en säker kluster, se [Service fabric ansluta till säker kluster](service-fabric-connect-to-secure-cluster.md).

Kör följande PowerShell-kommando för att ansluta till ett oskyddat kluster:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Exempel:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Steg 3: Ta fram Service Fabric Explorer
Nu du kan ansluta till klustret med Service Fabric Explorer antingen direkt från en av datorerna http://localhost:19080/Explorer/index.html eller via fjärranslutning med http://<*IPAddressofaMachine*>: 19080/Explorer / index.HTML.

## <a name="add-and-remove-nodes"></a>Lägga till och ta bort noder
Du kan lägga till eller ta bort noder i fristående Service Fabric-klustret när ditt företag behöver ändras. Se [lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md) detaljerade anvisningar.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Ta bort ett kluster
Om du vill ta bort ett kluster kör du PowerShell-skriptet *RemoveServiceFabricCluster.ps1* från paketmappen och lägger till sökvägen till JSON-konfigurationsfilen. Om du vill kan du ange en plats för loggen över borttagningen.

Det här skriptet kan köras på en dator som har administratörsåtkomst till alla datorer som noder i klustret konfigurationsfilen. Den dator som det här skriptet körs på behöver inte vara en del av klustret.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetridata som samlas in och hur du kan välja bort den
Samlar in telemetri i Service Fabric-syntax för att förbättra produkten som standard produkten. Best Practices Analyzer som körs som en del av installationen kontrollerar anslutningen till [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Om den inte kan nås, kommer installationen att misslyckas om du avanmäler dig från telemetri.

1. Telemetri pipeline försöker överföra data till följande [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) varje gång om dagen. Det är en bästa överföring och har ingen inverkan på funktionen kluster. Telemetrin skickas endast från noden som kör failover manager primära. Inga andra noder skicka ut telemetri.
2. Telemetrin består av följande:

* Antal tjänster
* Antal ServiceTypes
* Antal program
* Antal ApplicationUpgrades
* Antal FailoverUnits
* Antal InBuildFailoverUnits
* Antal UnhealthyFailoverUnits
* Antal repliker
* Antal InBuildReplicas
* Antal StandByReplicas
* Antal OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Antalet noder
* IsContextComplete: True/False
* ClusterId: Detta är ett GUID som genereras slumpmässigt för varje kluster
* ServiceFabricVersion
* IP-adressen för den virtuella datorn eller datorn från vilken telemetrin har överförts

Lägg till följande om du vill inaktivera telemetri *egenskaper* i klustret config: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Förhandsgranskningsfunktioner som ingår i det här paketet
Ingen.


> [!NOTE]
> Från och med den nya [GA-versionen av fristående kluster för Windows Server (version 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), kan du uppgradera ditt kluster till kommande versioner manuellt eller automatiskt. Referera till [uppgradera en fristående Service Fabric-kluster version](service-fabric-cluster-upgrade-windows-server.md) dokument för mer information.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Distribuera och ta bort program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)
* [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Lägg till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uppgradera en fristående Service Fabric-kluster-version](service-fabric-cluster-upgrade-windows-server.md)
* [Skapa ett fristående Service Fabric-kluster med Azure virtuella datorer som kör Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Skydda ett fristående kluster på Windows med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md)
* [Skydda ett fristående kluster på Windows med X509 certifikat](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
