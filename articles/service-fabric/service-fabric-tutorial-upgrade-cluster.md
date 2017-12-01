---
title: Uppgradera Azure Service Fabric runtime | Microsoft Docs
description: "Lär dig hur du använder PowerShell för att uppgradera körtiden för en värd för Azure Service Fabric-klustret."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.openlocfilehash: 5057a741a60bf9632f15dbc47236791e06f5bf6b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="upgrade-the-runtime-of-a-service-fabric-cluster"></a>Uppgraderingen av körtiden för Service Fabric-kluster

Den här kursen ingår tre av en serie och visar hur du uppgraderar Service Fabric-körningsmiljön på ett Azure Service Fabric-kluster. Den här självstudiekursen delen är skriven för Service Fabric-kluster som körs på Azure och gäller inte för fristående Service Fabric kluster.

> [!WARNING]
> Den här delen av kursen kräver PowerShell. Stöd för uppgradering av klustret runtime ännu stöds inte av Azure CLI-verktygen. Du kan också kan ett kluster uppgraderas på portalen. Mer information finns i [uppgradera ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md).

Om klustret redan körs den senaste Service Fabric runtime, behöver du inte göra det här steget. Den här artikeln kan dock användas för att installera alla stöds körning på ett Azure Service Fabric-kluster.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Läsa kluster-version
> * Ange version för kluster

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure med hjälp av en mall
> * [Skala ett kluster in eller ut](service-fabric-tutorial-scale-cluster.md)
> * Uppgraderingen av körtiden för ett kluster
> * [Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera den [Azure Powershell Modulversion 4.1 eller högre](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
- Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och **Azure-utveckling**, **ASP.NET och web development**, och **.NET Core plattformsoberoende development**arbetsbelastningar.  Ställa in en [.NET utvecklingsmiljö](service-fabric-get-started.md).
- Om du distribuerar en Linux-kluster måste du ställa in en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera den [Service Fabric CLI](service-fabric-cli.md). 

### <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på ditt Azure-konto väljer din prenumeration innan du kan köra kommandon för Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Hämta versionen av körningsmiljön

När du har anslutit till Azure valt prenumerationen som innehåller Service Fabric-kluster, som du kan hämta versionen av körningsmiljön i klustret.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Eller bara ha en lista över alla kluster i din prenumeration med följande:

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Observera den **ClusterCodeVersion** värde. Det här värdet används i nästa avsnitt.

## <a name="upgrade-the-runtime"></a>Uppgradera körningsmiljön

Använd värdet för **ClusterCodeVersion** från föregående avsnitt med den `Get-ServiceFabricRuntimeUpgradeVersion` för att identifiera vilka versioner som är tillgängliga för att uppgradera till. Denna cmdlet kan endast köras från en dator som är ansluten till internet. Om du vill se vilka runtime-versioner som du kan uppgradera till från version exempelvis `5.7.198.9494`, använder du följande kommando:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Du kan se Azure Service Fabric-klustret för att uppgradera till en nyare runtime med en lista över versioner. Till exempel om version `6.0.219.9494` går att uppgradera för att använda följande kommando för att uppgradera ditt kluster.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> Klustret runtime uppgraderingen kan ta lång tid att slutföra. PowerShell blockeras när uppgraderingen är igång. Du kan använda en annan PowerShell-session för att kontrollera status för uppgraderingen.

Status för uppgraderingen kan övervakas med antingen PowerShell eller `sfctl` CLI.

Först ansluta till klustret med SSL-certifikat som skapas i den första delen av kursen. Använd den `Connect-ServiceFabricCluster` cmdlet eller `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Använd sedan `Get-ServiceFabricClusterUpgrade` eller `sfctl cluster upgrade-status` att visa status. Något som liknar följande resultat visas.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="conclusion"></a>Slutsats
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Hämta versionen av körningsmiljön kluster
> * Uppgradera kluster runtime
> * Övervaka uppgraderingen

Gå sedan till följande guiden för att lära dig hur du distribuerar API-hantering med ett Service Fabric-kluster.
> [!div class="nextstepaction"]
> [Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)
