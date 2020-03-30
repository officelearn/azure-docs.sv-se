---
title: Skapa ett fristående Azure Service Fabric-kluster
description: Skapa ett Azure Service Fabric-kluster på alla datorer (fysiska eller virtuella) som kör Windows Server, oavsett om det är lokalt eller i något moln.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258829"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Skapa ett fristående kluster som körs på Windows Server
Du kan använda Azure Service Fabric för att skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server. Det innebär att du kan distribuera och köra Service Fabric-program i alla miljöer som innehåller en uppsättning sammankopplade Windows Server-datorer, oavsett om det är lokalt eller med alla molnleverantörer. Service Fabric tillhandahåller ett installationspaket för att skapa Service Fabric-kluster som kallas det fristående Windows Server-paketet. Traditionella Service Fabric-kluster på Azure är tillgängliga som en hanterad tjänst, medan fristående Service Fabric-kluster är självbetjäning. Mer information om skillnaderna finns i [Jämföra Azure- och fristående Service Fabric-kluster](./service-fabric-deploy-anywhere.md).

I den här artikeln får du hjälp med stegen för att skapa ett fristående service fabric-kluster.

> [!NOTE]
> Det här fristående Windows Server-paketet är kommersiellt tillgängligt utan kostnad och kan användas för produktionsdistributioner. Det här paketet kan innehålla nya Service Fabric-funktioner som finns i "Preview". Bläddra ned till "[Förhandsgranskningsfunktioner som ingår i det här paketet](#previewfeatures_anchor)." för listan över förhandsgranskningsfunktionerna. Du kan [ladda ner en kopia av licensavtalet](https://go.microsoft.com/fwlink/?LinkID=733084) nu.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Få support för Service Fabric för Windows Server-paketet
* Fråga communityn om det fristående servicepaketet för Windows Server i [Azure Service Fabric-forumet](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Öppna en biljett för [Professionell support för Service Fabric.](https://support.microsoft.com/oas/default.aspx?prid=16146)  Läs mer om Professionell support från Microsoft [här](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Du kan också få support för det här paketet som en del av [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Mer information finns i [supportalternativ för Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Om du vill samla in loggar för supportändamål kör du [servicetygsmonteraren](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Ladda ned Service Fabric för Windows Server-paketet
Om du vill skapa klustret använder du Service Fabric för Windows Server-paketet (Windows Server 2012 R2 och nyare) som finns här: <br>
[Download Link - Service Fabric Fristående paket - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Här hittar du information om innehållet i [paketet](service-fabric-cluster-standalone-package-contents.md).

Service Fabric-körningspaketet hämtas automatiskt när klustret skapas. Om du distribuerar från en dator som inte är ansluten till internet, ladda ner runtime-paketet från bandet härifrån: <br>
[Hämta länk - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Hitta fristående klusterkonfigurationsexempel på: <br>
[Fristående klusterkonfigurationsexempel](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Skapa klustret
Flera exempelkonfigurationsfiler för kluster installeras med konfigurationspaketet. *ClusterConfig.Unsecure.DevCluster.json* är den enklaste klusterkonfigurationen: ett oskyddat kluster med tre noder som körs på en enskild dator.  Andra konfigurationsfiler beskriver kluster med en eller flera datorer som skyddas med X.509-certifikat eller Windows-säkerhet.  Du behöver inte ändra några av standardinställningarna i den här självstudien, men titta igenom konfigurationsfilen så att du bekantar dig med inställningarna.  I avsnittet **Noder** beskrivs de tre noderna i klustret: namn, IP-adress, [nodtyp, feldomän och uppgraderingsdomän](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  I avsnittet **Egenskaper** definieras [säkerhet, tillförlitlighetsnivå, diagnostiksamling och nodtyper](service-fabric-cluster-manifest.md#cluster-properties) för klustret.

Klustret som skapas i den här artikeln är osäkert.  Vem som helst kan ansluta anonymt och utföra hanteringsåtgärder, så produktionskluster bör alltid skyddas med X.509-certifikat eller Windows-säkerhet.  Säkerhetsfunktioner konfigureras bara när kluster skapas, och du kan inte aktivera någon säkerhet i efterhand. Uppdatera konfigurationsfilen aktivera [certifikatsäkerhet](service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet](service-fabric-windows-cluster-windows-security.md). Mer information om klustersäkerheten med Service Fabric finns i avsnittet om hur du [skyddar ett kluster](service-fabric-cluster-security.md).

### <a name="step-1-create-the-cluster"></a>Steg 1: Skapa klustret

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenario A: Skapa ett osäkert lokalt utvecklingskluster
Service Fabric kan distribueras till ett endatorsutvecklingskluster med hjälp av filen *ClusterConfig.Unsecure.DevCluster.json* som ingår i [Samples](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Packa upp det fristående paketet till datorn, kopiera exempelkonfigurationsfilen till den lokala datorn och kör sedan *Skriptet CreateServiceFabricCluster.ps1* via en powershell-administratörssession från den fristående paketmappen.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Se avsnittet Miljöinställningar på [Planera och förbered klusterdistributionen](service-fabric-cluster-standalone-deployment-preparation.md) för felsökningsinformation.

Om du är klar med utvecklingsscenarierna kan du ta bort service fabric-klustret från datorn genom att referera till steg i avsnittet "[Ta bort ett kluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenario B: Skapa ett kluster med flera datorer
När du har gått igenom planerings- och förberedelsestegen som beskrivs i [Planera och förbereda klusterdistributionen](service-fabric-cluster-standalone-deployment-preparation.md)är du redo att skapa produktionsklustret med hjälp av klusterkonfigurationsfilen.

Klusteradministratören som distribuerar och konfigurerar klustret måste ha administratörsbehörighet på datorn. Du kan inte installera Service Fabric på en domänkontrollant.

1. Skriptet *TestConfiguration.ps1* i det fristående paketet används för att analysera bästa praxis och för att kontrollera om ett kluster kan distribueras i en viss miljö. [Distributionsförberedelser](service-fabric-cluster-standalone-deployment-preparation.md) visar krav och förutsättningar. Kör skriptet för att kontrollera om du kan skapa utvecklingsklustret:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Du bör se utdata som liknar följande. Om det nedre fältet "Passed" returneras som "True" har sanity-kontroller skickats och klustret ser ut att kunna distribueras baserat på indatakonfigurationen.

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

2. Skapa klustret: Kör skriptet *CreateServiceFabricCluster.ps1* för att distribuera Service Fabric-klustret över varje dator i konfigurationen. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Distributionsspårningar skrivs till den virtuella dator där du körde PowerShell-skriptet CreateServiceFabricCluster.ps1. Dessa finns i undermappen DeploymentTraces i katalogen som skriptet kördes från. Om du vill se ifall Service Fabric distribuerades korrekt till en dator, letar du reda på de installerade filerna i katalogen FabricDataRoot enligt anvisningarna i klusterkonfigurationsfilens FabricSettings-avsnitt (som standard c:\ProgramData\SF). Dessutom kan man se att processerna FabricHost.exe och Fabric.exe körs i Aktivitetshanteraren.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenario C: Skapa ett offline-kluster (internet frånkopplad)
Service Fabric-körningspaketet hämtas automatiskt när klustret skapas. När du distribuerar ett kluster till datorer som inte är anslutna till internet måste du hämta service fabric-körningspaketet separat och ange sökvägen till det när kluster skapas.
Runtime-paketet kan laddas ner separat, från en annan maskin som är ansluten till Internet, på [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Kopiera körningspaketet till den plats där du distribuerar offlineklustret från och skapa klustret genom att köra `CreateServiceFabricCluster.ps1` med `-FabricRuntimePackagePath` parametern som ingår, som visas i det här exemplet: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* och *.\MicrosoftAzureServiceFabric.cab* är sökvägarna till klusterkonfigurationen respektive körnings-CAB-filen.

### <a name="step-2-connect-to-the-cluster"></a>Steg 2: Anslut till klustret
Anslut till klustret för att kontrollera att klustret körs och är tillgängligt. ServiceFabric PowerShell-modulen installeras med runtime.  Du kan ansluta till klustret från en av klusternoderna eller från en fjärrdator med Service Fabric-körningen.  Cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) upprättar en anslutning till klustret.

Om du vill ansluta till ett osäkert kluster kör du följande PowerShell-kommando:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Ett exempel:
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

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Steg 3: Visualisera klustret med Hjälp av Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett bra verktyg för att visualisera klustret och hantera program.  Service Fabric Explorer är en tjänst som körs i klustret [http://localhost:19080/Explorer](http://localhost:19080/Explorer)och som du kommer åt med hjälp av en webbläsare genom att navigera till .

Instrumentpanelen för klustret innehåller en översikt över klustret, inklusive en sammanfattning av program- och nodhälsan. Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Lägga till och ta bort noder
Du kan lägga till eller ta bort noder i det fristående Service Fabric-klustret när ditt företag behöver ändras. Se [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md) för detaljerade anvisningar.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Ta bort ett kluster
Om du vill ta bort ett kluster kör du PowerShell-skriptet *RemoveServiceFabricCluster.ps1* från paketmappen och lägger till sökvägen till JSON-konfigurationsfilen. Om du vill kan du ange en plats för loggen över borttagningen.

Det här skriptet kan köras på alla datorer som har administratörsbehörighet till alla datorer som visas som noder i klusterkonfigurationsfilen. Datorn som skriptet körs på behöver inte vara en del av klustret.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetridata som samlas in och hur man väljer bort dem
Som standard samlar produkten in telemetri på användningen av Service Fabric för att förbättra produkten. Best Practice Analyzer som körs som en del av [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)installationen söker efter anslutning till . Om den inte kan nås misslyckas installationen om du inte väljer bort telemetri.

1. Telemetripipelinen försöker överföra följande [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) data till en gång varje dag. Det är en bästa möjliga uppladdning och har ingen inverkan på klusterfunktionen. Telemetrin skickas endast från noden som kör redundanshanterarens primära. Inga andra noder skickar ut telemetri.
2. Telemetrin består av följande:

* Antal tjänster
* Antal ServiceTypes
* Antal ansökningar
* Antal ApplicationUpgrades
* Antal redundansenheter
* Antal InBuildFailoverUnits
* Antal ohälsosammaFailoverUnits
* Antal repliker
* Antal InBuildReplicas
* Antal StandByReplicas
* Antal OfflineReplicas
* VanligaQueueLength
* FrågaQueueLength
* RedundansUnitQueueLength
* BegåQueueLength
* Antal noder
* IsContextComplete: Sant/Falskt
* ClusterId: Detta är ett GUID som genereras slumpmässigt för varje kluster
* ServiceFabricVersion
* IP-adressen för den virtuella datorn eller datorn från vilken telemetrin laddas upp

Om du vill inaktivera telemetri lägger du till följande *i egenskaper* i klusterkonfigurationen: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Förhandsgranska funktioner som ingår i det här paketet
Inga.


> [!NOTE]
> Från och med den nya [GA-versionen av det fristående klustret för Windows Server (version 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)kan du uppgradera klustret till framtida versioner, manuellt eller automatiskt. Mer information finns i Uppgradera ett fristående klusterversionsdokument för [Service Fabric.Se Uppgradera ett fristående service fabric-klusterversionsdokument](service-fabric-cluster-upgrade-windows-server.md) för mer information.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Distribuera och ta bort program med PowerShell](service-fabric-deploy-remove-applications.md)
* [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uppgradera en fristående service fabric-klusterversion](service-fabric-cluster-upgrade-windows-server.md)
* [Skapa ett fristående Service Fabric-kluster med virtuella Azure-datorer som kör Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Skydda ett fristående kluster i Windows med Windows-säkerhet](service-fabric-windows-cluster-windows-security.md)
* [Skydda ett fristående kluster i Windows med X509-certifikat](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
