---
title: Uppgradera Azure Service Fabric-körning (exekvering)| Microsoft Docs
description: I den här guiden får du lära dig hur du använder PowerShell och uppgraderar körningen för ett Service Fabric-kluster med Azure som värd.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 407268299e77d771a53c49c11995dce1ada65112
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster"></a>Självstudieguide: uppgradera körningen av ett Service Fabric-kluster

Den här guiden är del tre i en serie. Den visar hur du uppgraderar körningen av Service Fabric ett Azure Service Fabric-kluster. Den här delen är skriven för Service Fabric-kluster som körs på Azure och gäller inte för fristående Service Fabric kluster.

> [!WARNING]
> För den delen av kursen krävs PowerShell. Stöd för uppgradering av klusterkörning finns inte i Azure CLI-verktygen ännu. Ett kluster kan också uppgraderas på portalen. Mer information finns i [Uppgradera till ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md).

Om klustret redan körs med senaste Service Fabric-körningen behöver du inte genomföra det här steget. Den här artikeln kan dock användas för att installera alla stödda körningar på ett Azure Service Fabric-kluster.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * läser klusterversionen
> * ställer in klusterversionen.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure med hjälp av en mall
> * [skala upp eller ned ett kluster](service-fabric-tutorial-scale-cluster.md)
> * uppgradera körningen för ett kluster
> * [distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här självstudien:
- om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera [Azure Powershell-modulen version 4.1 eller senare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
- Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och arbetsbelastningarna **Azure Development**, **ASP.NET och webbutveckling** samt **.NET Core plattformsoberoende utveckling**.  Konfigurera sedan en [.NET-utvecklingsmiljö](service-fabric-get-started.md).
- Om du distribuerar ett Linux-kluster måste du konfigurera en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera [Service Fabric CLI](service-fabric-cli.md). 

### <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på ditt Azure-konto och välj din prenumeration innan du kör Azure-kommandon.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Hämta körningsversion

När du har anslutit till Azure, och valt prenumerationen som innehåller Service Fabric-klustret, kan du hämta körningsversionen av klustret.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Du kan också bara skaffa en lista över alla kluster i prenumerationen så här:

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Notera värdet för **ClusterCodeVersion**. Det här värdet används i nästa avsnitt.

## <a name="upgrade-the-runtime"></a>Uppgradera körningsmiljön

Använd värdet för **ClusterCodeVersion** från föregående avsnitt tillsammans med cdmlet `Get-ServiceFabricRuntimeUpgradeVersion` för att identifiera vilka versioner du kan uppgradera till. Denna cmdlet kan endast köras från en dator som är ansluten till internet. Om du till exempel vill se vilka körningsversioner du kan uppgradera till från version `5.7.198.9494` använder du följande kommando:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Med en versionslista kan du instruera Azure Service Fabric-klustret att uppgradera till en nyare körning. Om till exempel version `6.0.219.9494` är tillgänglig använder du följande kommando för att uppgradera klustret.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> Uppgraderingen av klusterkörningen kan ta lång tid att slutföra. PowerShell blockeras när uppgraderingen körs. Du kan använda en annan PowerShell-session för att kontrollera uppgraderingens status.

Status för uppgraderingen kan övervakas med antingen PowerShell eller CLI `sfctl`.

Börja med att ansluta till klustret med SSL-certifikatet som skapades i den första delen av kursen. Använd cdmlet `Connect-ServiceFabricCluster` eller `sfctl cluster upgrade-status`.

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

Använd sedan `Get-ServiceFabricClusterUpgrade` eller `sfctl cluster upgrade-status` för att visa statusen. Något som liknar följande resultat visas.

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

## <a name="conclusion"></a>Sammanfattning
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Hämta versionen av klusterkörningen
> * Uppgradera körningstiden för kluster
> * Övervaka uppgraderingen

Gå sedan till följande självstudiekurs och lär dig hur du distribuerar API-hantering med ett Service Fabric-kluster.
> [!div class="nextstepaction"]
> [distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md).
