---
title: Skapa en regel för ett kluster för Azure belastningsutjämnare
description: Konfigurera en Azure belastningsutjämnare för att öppna portar för Azure Service Fabric-klustret.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 53dcd6c0705faa94e83d6e44f813fa9c575843e8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Öppna portar för ett Service Fabric-kluster

Belastningsutjämnaren distribueras med Azure Service Fabric-klustret dirigerar trafik till din app som körs på en nod. Om du ändrar din app om du vill använda en annan port måste du exponera porten (eller vidarebefordra en annan port) i Azure-belastningsutjämnaren.

När du har distribuerat Service Fabric-kluster till Azure skapades automatiskt en belastningsutjämnare för dig. Om du inte har en belastningsutjämnare, se [konfigurera en Internetriktade belastningsutjämnare](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Konfigurera service fabric

Service Fabric-programmet **ServiceManifest.xml** config-fil som definierar de slutpunkter som ditt program förväntar sig att använda. När konfigurationsfilen har uppdaterats för att definiera en slutpunkt, belastningsutjämnaren måste uppdateras för att exponera som (eller en annan) port. Mer information om hur du skapar service fabric-slutpunkt finns [konfigurera en slutpunkt](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Skapa en belastningsutjämningsregel

En regel för belastningsutjämnare öppnar en port mot internet och vidarebefordrar trafik till noden Intern port som används av ditt program. Om du inte har en belastningsutjämnare, se [konfigurera en Internetriktade belastningsutjämnare](..\load-balancer\load-balancer-get-started-internet-portal.md).

Om du vill skapa en regel för belastningsutjämnare som du behöver samla in följande information:

- Belastningsutjämnarens namn.
- Resursgruppen för det belastningen belastningsutjämnare och service fabric-klustret.
- Externa porten.
- Intern port.

## <a name="azure-cli"></a>Azure CLI
Det tar bara ett enda kommando för att skapa en regel för belastningsutjämnare med den **Azure CLI**. Du behöver veta över belastningen belastningsutjämnare och resurs gruppen för att skapa en ny regel.

>[!NOTE]
>Om du behöver ta reda på namnet för belastningsutjämnaren kan du använda kommandot för att snabbt visa en lista över alla belastningsutjämnare och associerade resursgrupper.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI-kommandot har några parametrar som beskrivs i följande tabell:

| Parameter | Beskrivning |
| --------- | ----------- |
| `--backend-port`  | Port Service Fabric-programmet lyssnar på. |
| `--frontend-port` | Port belastningsutjämnaren visar för externa anslutningar. |
| `-lb-name` | Namnet på belastningsutjämnaren om du vill ändra. |
| `-g`       | Den resursgrupp som har både belastningsutjämnare och Service Fabric-klustret. |
| `-n`       | Önskat namn för regeln. |


>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med Azure CLI finns [skapar en belastningsutjämnare med Azure CLI](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell är lite mer komplicerat än Azure CLI. Så här konceptuella om du vill skapa en regel:

1. Hämta belastningsutjämnaren från Azure.
2. Skapa en regel.
3. Lägga till regeln till belastningsutjämnaren.
4. Uppdatera belastningsutjämnaren.

>[!NOTE]
>Om du behöver ta reda på namnet för belastningsutjämnaren kan du använda kommandot för att snabbt visa en lista över alla belastningsutjämnare och associerade resursgrupper.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Om den `New-AzureRmLoadBalancerRuleConfig` kommando, den `-FrontendPort` representerar porten belastningsutjämnaren visar för externa anslutningar och `-BackendPort` representerar service fabric-appen lyssnar på porten.

>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med PowerShell finns [skapar en belastningsutjämnare med PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [nätverk i Service Fabric](service-fabric-patterns-networking.md).