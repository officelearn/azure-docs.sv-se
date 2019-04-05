---
title: Skapa en Azure Load Balancer-regel för ett kluster
description: Konfigurera en Azure Load Balancer för att öppna portar för Azure Service Fabric-klustret.
services: service-fabric
documentationcenter: na
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: d95d2802398a61b948ff6c59fb3eab0e1ddddbc5
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051673"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Öppna portar för Service Fabric-kluster

Belastningsutjämnaren som distribueras med Azure Service Fabric-klustret dirigerar trafik till din app som körs på en nod. Om du ändrar din app om du vill använda en annan port måste du exponera den porten (eller vidarebefordra en annan port) i Azure Load Balancer.

När du har distribuerat din Service Fabric-kluster till Azure skapades automatiskt en belastningsutjämnare för dig. Om du inte har en belastningsutjämnare, se [konfigurera en internetuppkopplad belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurera service fabric

Service Fabric-programmet **ServiceManifest.xml** config-filen definierar de slutpunkter som ditt program förväntar sig att använda. När konfigurationsfilen har uppdaterats för att definiera en slutpunkt, belastningsutjämnaren måste uppdateras om du vill exponera som (eller en annan) port. Läs mer om hur du skapar service fabric-slutpunkten, [konfigurera en slutpunkt](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En belastningsutjämningsregel öppnar en port för webbservergrupper på internet och vidarebefordrar trafik till noden Intern port som används av ditt program. Om du inte har en belastningsutjämnare, se [konfigurera en internetuppkopplad belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md).

Om du vill skapa en belastningsutjämningsregel, måste du samla in följande information:

- Namnet på belastningsutjämnaren.
- Resursgruppen för det load balancer och service fabric-klustret.
- Extern port.
- Intern port.

## <a name="azure-cli"></a>Azure CLI
Det tar bara ett enda kommando för att skapa en regel för belastningsutjämnaren med den **Azure CLI**. Du behöver bara veta både namnet på den load balancer och resursgruppen att skapa en ny regel.

>[!NOTE]
>Om du behöver att fastställa namnet på belastningsutjämnaren kan använda det här kommandot att snabbt få en lista över alla belastningsutjämnare och associerade resursgrupper.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI-kommando har några parametrar som beskrivs i följande tabell:

| Parameter | Beskrivning |
| --------- | ----------- |
| `--backend-port`  | Port Service Fabric-programmet lyssnar på. |
| `--frontend-port` | Port belastningsutjämnaren exponerar för externa anslutningar. |
| `-lb-name` | Namnet på belastningsutjämnaren för att ändra. |
| `-g`       | Den resursgrupp som har både belastningsutjämnare och Service Fabric-kluster. |
| `-n`       | Önskat namn för regeln. |


>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med Azure CLI finns i [skapar en belastningsutjämnare med Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell är lite mer komplicerat än Azure CLI. Så här konceptuella för att skapa en regel:

1. Hämta belastningsutjämnaren från Azure.
2. Skapa en regel.
3. Lägga till regeln till belastningsutjämnaren.
4. Uppdatera belastningsutjämnaren.

>[!NOTE]
>Om du vill fastställa namnet på belastningsutjämnaren kan du använda det här kommandot för att snabbt få en lista över alla belastningsutjämnare och associerade resursgrupper.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Om den `New-AzLoadBalancerRuleConfig` kommandot, den `-FrontendPort` representerar porten belastningsutjämnaren exponerar för externa anslutningar och `-BackendPort` representerar den port som service fabric-app lyssnar på.

>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med PowerShell finns i [skapar en belastningsutjämnare med PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [nätverksfunktionerna i Service Fabric](service-fabric-patterns-networking.md).rvice-fabric-mönster – networking.md).