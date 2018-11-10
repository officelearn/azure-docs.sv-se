---
title: Självstudie om installation av fristående Service Fabric-klient – Azure Service Fabric | Microsoft Docs
description: I den här självstudien lär du dig att installera den fristående Service Fabric-klienten på klustret som du skapade i den föregående självstudien.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: b740aa205982bc0c1104b59e3ea89e2d5980d461
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240349"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Självstudie: Installera och skapa Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja miljö och skapa kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudieserien skapar du ett fristående kluster som hanteras av AWS och installerar ett program i det.

Den här självstudien är del två i en serie. Självstudien vägleder dig genom stegen för att skapa ett fristående Service Fabric-kluster.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Ladda ned och installera det fristående Service Fabric-paketet
> * Skapa Service Fabric-klustret
> * Ansluta till Service Fabric-klustret

## <a name="download-the-service-fabric-for-windows-server-package"></a>Ladda ned Service Fabric för Windows Server-paketet

Fabric Service tillhandahåller ett konfigurationspaket för att skapa fristående Service Fabric-kluster.  [Ladda ned installationspaketet](https://go.microsoft.com/fwlink/?LinkId=730690) på den lokala datorn.  När det har laddats ned, kopierar du det via RDP-anslutningen till din EC2-instans och klistrar in det på skrivbordet.

Välj zip-filen, öppna snabbmenyn och välj **Extrahera alla** > **Extrahera**.  När du extraherar filerna skapas en mapp på skrivbordet med samma namn som zip-filen.

Om du vill kan du hämta mer information om [innehållet i installationspaketet](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Konfigurera konfigurationsfilen

Du utvecklar ett Windows-kluster med tre noder, så du måste ändra filen `ClusterConfig.Unsecure.MultiMachine.json`.

Därefter uppdaterar du de tre ipAddress-raderna som finns i filen på raderna 8, 15 och 22 till IP-adresserna för varje instans.

När du har uppdaterat noderna visas de på följande sätt:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Du måste därefter uppdatera ett par av egenskaperna.  På rad 34 måste du ändra anslutningssträngen för diagnostiklagret, som ska se ut så här `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Slutligen går du till avsnittet `nodeTypes` i konfigurationen och lägger till ett nytt avsnitt för att mappa de tillfälliga portarna som ska användas av Windows.  Konfigurationsfilen bör likna följande:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Validera miljön

Skriptet *TestConfiguration.ps1* i det fristående paketet används för att analysera bästa praxis och för att kontrollera om ett kluster kan distribueras i en viss miljö. [Distributionsförberedelser](service-fabric-cluster-standalone-deployment-preparation.md) visar krav och förutsättningar. Kör skriptet för att kontrollera om du kan skapa utvecklingsklustret:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Du bör se utdata som ser ut så här. Om det nedre fältet ”Godkänd” returneras som `True`, har hälsokontrollerna godkänts och klustret ser ut att kunna distribueras baserat på indatakonfigurationen.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Skapa klustret

När du har verifierat klustrets konfiguration kör du skriptet *CreateServiceFabricCluster.ps1* för att distribuera Service Fabric-klustret till de virtuella datorerna i konfigurationsfilen.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Om allt fungerar får du utdata som ser ut så här:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Distributionsspårningar skrivs till den virtuella dator där du körde PowerShell-skriptet CreateServiceFabricCluster.ps1. Dessa finns i undermappen DeploymentTraces i katalogen som skriptet kördes från. Om du vill se ifall Service Fabric distribuerades korrekt till en dator, letar du reda på de installerade filerna i katalogen FabricDataRoot enligt anvisningarna i klusterkonfigurationsfilens FabricSettings-avsnitt (som standard c:\ProgramData\SF). Dessutom kan man se att processerna FabricHost.exe och Fabric.exe körs i Aktivitetshanteraren.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Hämta Service Fabric Explorer

Nu kan du ansluta till klustret med Service Fabric Explorer, antingen direkt från en av datorerna med http://localhost:19080/Explorer/index.html eller via en fjärranslutning med http://<*IPAddressofaMachine*>: 19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Lägga till och ta bort noder

Du kan lägga till eller ta bort noder i det fristående Service Fabric-klustret när ditt företag behöver ändras. Se [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md) för detaljerade anvisningar.

## <a name="next-steps"></a>Nästa steg

I den andra delen i serien har du lärt dig hur du överför stora mängder slumpmässiga data parallellt till lagringskontot, till exempel hur du gör för att:

> [!div class="checklist"]
> * Konfigurera anslutningssträngen
> * Skapa programmet
> * Köra programmet
> * Verifiera antalet anslutningar

Gå vidare till avsnitt tre i serien för att installera ett program i klustret som du skapade.

> [!div class="nextstepaction"]
> [Installera programmet i Service Fabric-klustret](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
