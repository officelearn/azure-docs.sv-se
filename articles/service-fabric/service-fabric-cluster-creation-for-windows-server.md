---
title: Skapa ett fristående Azure Service Fabric-kluster | Microsoft Docs
description: Skapa ett Azure Service Fabric-kluster på valfri dator (fysisk eller virtuell) som kör Windows Server, oavsett om det är en lokal eller i ett moln.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: dekapur
ms.openlocfilehash: f91a6b305a3d531aa1c733685f6d896ed07054ae
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257613"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Skapa ett fristående kluster som körs på Windows Server
Du kan använda Azure Service Fabric för att skapa Service Fabric-kluster på några virtuella datorer eller datorer som kör Windows Server. Det innebär att du kan distribuera och köra Service Fabric-program i alla miljöer som innehåller en uppsättning sammankopplade Windows Server-datorer, oavsett om det är lokalt eller med någon annan molnleverantör. Service Fabric tillhandahåller ett konfigurationspaket för att skapa Service Fabric-kluster kallas det fristående paketet för Windows Server.

Den här artikeln vägleder dig genom stegen för att skapa fristående Service Fabric-kluster.

> [!NOTE]
> Det här fristående Windows Server-paketet är kommersiellt tillgängligt och kan användas för Produktionsdistribution. Det här paketet kan innehålla nya Service Fabric-funktioner som finns i ”förhandsversion”. Rulla ned till ”[Förhandsversionsfunktioner som ingår i det här paketet](#previewfeatures_anchor)”. avsnittet för förhandsfunktionerna i listan. Du kan [hämta en kopia av LICENSVILLKOREN](https://go.microsoft.com/fwlink/?LinkID=733084) nu.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Få support för Service Fabric för Windows Server-paket
* Fråga communityn om det fristående paketet för Service Fabric för Windows Server i den [Azure Service Fabric-forumet](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Öppna en biljett för [Professional Support för Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Mer information om professionell Support från Microsoft [här](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Du kan också få support för det här paketet som en del av [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Mer information finns i [supportalternativ för Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Om du vill samla in loggar för supportärenden, kör den [logginsamlaren för Service Fabric fristående](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Ladda ned Service Fabric för Windows Server-paketet
Du kan skapa klustret med Service Fabric för Windows Server-paket (Windows Server 2012 R2 och senare) finns här: <br>
[Hämta länk - Service Fabric fristående Package - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Ha information om innehållet i paketet [här](service-fabric-cluster-standalone-package-contents.md).

Service Fabric-körningspaketet laddas ned automatiskt när klustret skapas. Distribuera från en dator som inte är ansluten till internet, att hämta runtime-paketet out of band härifrån: <br>
[Hämta länk - Service Fabric-körningen – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Hitta fristående kluster Konfigurationsexempel på: <br>
[Konfigurationsexempel för fristående kluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Skapa klustret
Flera exempelkonfigurationsfiler för kluster installeras med konfigurationspaketet. *ClusterConfig.Unsecure.DevCluster.json* är den enklaste klusterkonfigurationen: ett oskyddat kluster med tre noder som körs på en enskild dator.  Andra konfigurationsfiler beskriver kluster med en eller flera datorer som skyddas med X.509-certifikat eller Windows-säkerhet.  Du behöver inte ändra några av standardinställningarna i den här självstudien, men titta igenom konfigurationsfilen så att du bekantar dig med inställningarna.  I avsnittet **Noder** beskrivs de tre noderna i klustret: namn, IP-adress, [nodtyp, feldomän och uppgraderingsdomän](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  I avsnittet **Egenskaper** definieras [säkerhet, tillförlitlighetsnivå, diagnostiksamling och nodtyper](service-fabric-cluster-manifest.md#cluster-properties) för klustret.

Klustret skapas i den här artikeln är inte säkert.  Vem som helst kan ansluta anonymt och utföra hanteringsåtgärder, så produktionskluster bör alltid skyddas med X.509-certifikat eller Windows-säkerhet.  Säkerhetsfunktioner konfigureras bara när kluster skapas, och du kan inte aktivera någon säkerhet i efterhand. Uppdatera konfigurationsfilen aktivera [certifikat security](service-fabric-windows-cluster-x509-security.md) eller [Windows security](service-fabric-windows-cluster-windows-security.md). Mer information om klustersäkerheten med Service Fabric finns i avsnittet om hur du [skyddar ett kluster](service-fabric-cluster-security.md).

### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Steg 1A: skapa ett oskyddat lokalt utvecklingskluster
Service Fabric kan distribueras till ett kluster för utveckling av en dator med hjälp av den *ClusterConfig.Unsecure.DevCluster.json* -filen som ingår i [exempel](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Packa upp det fristående paketet till din dator, kopiera exempel config-filen till den lokala datorn och kör sedan den *CreateServiceFabricCluster.ps1* skriptet via en administratör PowerShell-session från paketmappen fristående .

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Se avsnittet Konfigurera miljön på [planera och förbereda distributionen av klustret](service-fabric-cluster-standalone-deployment-preparation.md) för information om felsökning.

Om du är klar körs utvecklingsscenarier, du kan ta bort Service Fabric-klustret från datorn genom att referera till stegen i avsnittet ”[ta bort ett kluster](#removecluster_anchor)”. 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Steg 1B: skapa ett kluster med flera datorer
När du har gått igenom den planering och förberedelser som anges i [planera och förbereda distributionen av klustret](service-fabric-cluster-standalone-deployment-preparation.md), du är redo att skapa klustret produktion med hjälp av din konfigurationsfil för klustret.

Klusteradministratören som distribuerar och konfigurerar klustret måste ha administratörsbehörighet på datorn. Du kan inte installera Service Fabric på en domänkontrollant.

1. Skriptet *TestConfiguration.ps1* i det fristående paketet används för att analysera bästa praxis och för att kontrollera om ett kluster kan distribueras i en viss miljö. [Distributionsförberedelser](service-fabric-cluster-standalone-deployment-preparation.md) visar krav och förutsättningar. Kör skriptet för att kontrollera om du kan skapa utvecklingsklustret:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Du bör se utdata som liknar följande. Om fältet längst ned ”godkänt” returneras som ”True”, hälsokontroller godkänts och klustret ser ut för att kunna distribueras baserat på konfigurationen av indata.

    ```powershell
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
> Distributionsspårningar skrivs till den virtuella dator där du körde PowerShell-skriptet CreateServiceFabricCluster.ps1. Dessa finns i undermappen DeploymentTraces i katalogen som skriptet kördes från. Om du vill se ifall Service Fabric distribuerades korrekt till en dator, letar du reda på de installerade filerna i katalogen FabricDataRoot enligt anvisningarna i klusterkonfigurationsfilens FabricSettings-avsnitt (som standard c:\ProgramData\SF). Dessutom kan man se att processerna FabricHost.exe och Fabric.exe körs i Aktivitetshanteraren.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Steg 1C: skapa ett offline (internet frånkopplad)-kluster
Service Fabric-körningspaketet laddas ned automatiskt när klustret skapas. När du distribuerar ett kluster till datorer som inte är anslutna till internet, kommer du behöva ladda ned Service Fabric-körningspaketet separat och ange sökvägen till den när klustret skapas.
Runtime-paketet kan laddas ned separat, från en annan dator som är anslutna till internet, på [Hämta länk - Service Fabric-körningen – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Kopiera runtime-paketet till platsen där du distribuerar offline klustret från och skapa klustret genom att köra `CreateServiceFabricCluster.ps1` med den `-FabricRuntimePackagePath` parametern ingår, som visas i det här exemplet: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.JSON* och *.\MicrosoftAzureServiceFabric.cab* är respektive sökvägar till klusterkonfigurationen och runtime CAB-filen.

### <a name="step-2-connect-to-the-cluster"></a>Steg 2: Anslut till klustret
Anslut till klustret för att verifiera klustret finns och är tillgängliga. ServiceFabric PowerShell-modulen installeras med runtime.  Du kan ansluta till klustret från en av noderna i klustret eller från en fjärrdator med Service Fabric-körningen.  Cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) upprättar en anslutning till klustret.

Om du vill ansluta till ett oskyddat kluster, kör du följande PowerShell-kommando:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Exempel:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Fler exempel på hur du ansluter till ett kluster finns i [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md). När du har anslutit till klustret använder du cmdleten [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) för att visa en lista över noder i klustret och statusinformation för varje nod. **HealthState** bör vara *OK* för varje nod.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Steg 3: Visualisera klustret med Service Fabric explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett bra verktyg för att visualisera klustret och hantera program.  Service Fabric Explorer är en tjänst som körs i klustret, som du kommer åt med en webbläsare genom att gå till [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer).

Instrumentpanelen för klustret innehåller en översikt över klustret, inklusive en sammanfattning av program- och nodhälsan. Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Lägga till och ta bort noder
Du kan lägga till eller ta bort noder i det fristående Service Fabric-klustret när ditt företag behöver ändras. Se [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md) för detaljerade anvisningar.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>ta bort ett kluster.
Om du vill ta bort ett kluster kör du PowerShell-skriptet *RemoveServiceFabricCluster.ps1* från paketmappen och lägger till sökvägen till JSON-konfigurationsfilen. Om du vill kan du ange en plats för loggen över borttagningen.

Det här skriptet kan köras på en dator som har administratörsåtkomst till alla datorer som listas som noder i klustret konfigurationsfilen. Den dator som skriptet körs på behöver inte vara en del av klustret.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetridata samlas in och hur du välja bort den
Samlar in telemetri i Service Fabric-användning för att förbättra produkten som standard produkten. Best Practices Analyzer som körs som en del av installationen kontrollerar anslutningen till [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Om den inte kan nås, kommer installationen att misslyckas om inte du välja bort telemetri.

1. Telemetri pipelinen försöker ladda upp följande data till [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) en gång varje dag. Det är en mån ladda upp och har ingen inverkan på funktionen för klustret. Telemetri skickas endast från den nod som kör redundansväxlingen manager primära. Inga andra noder skicka ut telemetri.
2. Telemetri som består av följande:

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
* Antal noder
* IsContextComplete: SANT/FALSKT
* ClusterId: Det här är ett GUID som slumpmässigt genererat för varje kluster
* ServiceFabricVersion
* IP-adressen för den virtuella datorn eller datorn från vilken telemetri som har överförts

Om du vill inaktivera telemetri, lägger du till följande till *egenskaper* i klustret config: *Konfigurationsparametern: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Förhandsversionsfunktioner som ingår i det här paketet
Ingen.


> [!NOTE]
> Från och med den nya [GA-versionen av fristående kluster för Windows Server (version 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), du kan uppgradera ditt kluster till framtida versioner manuellt eller automatiskt. Referera till [uppgraderar en fristående Service Fabric-kluster](service-fabric-cluster-upgrade-windows-server.md) dokument för mer information.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Distribuera och ta bort program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)
* [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Lägga till eller ta bort noder till ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uppgradera en fristående Service Fabric-kluster](service-fabric-cluster-upgrade-windows-server.md)
* [Skapa ett fristående Service Fabric-kluster med Azure virtuella datorer som kör Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Skydda ett fristående kluster i Windows med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md)
* [Skydda ett fristående kluster i Windows med hjälp av X509 certifikat](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png