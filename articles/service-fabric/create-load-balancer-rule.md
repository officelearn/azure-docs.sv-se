---
title: Skapa en Azure Load Balancer-regel för ett kluster
description: Konfigurera en Azure Load Balancer för att öppna portar för ditt Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025392"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Öppna portar för ett Service Fabric-kluster

Belastningsutjämnaren som distribueras med ditt Azure Service Fabric-kluster dirigerar trafik till din app som körs på en nod. Om du ändrar din app för att använda en annan port måste du visa den porten (eller dirigera en annan port) i Azure Load Balancer.

När du distribuerade ditt Service Fabric-kluster till Azure skapades en belastningsutjämnare automatiskt åt dig. Om du inte har någon belastningsutjämnare läser [du Konfigurera en Internet-vänd belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurera tjänstinfrastruktur

Filen Service Fabric-programmet **ServiceManifest.xml** config definierar de slutpunkter som programmet förväntar sig att använda. När konfigurationsfilen har uppdaterats för att definiera en slutpunkt måste belastningsutjämnaren uppdateras för att exponera den (eller en annan) port. Mer information om hur du skapar slutpunkten för tjänstinfrastruktur finns i [Konfigurera en slutpunkt](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En load balancer-regel öppnar en internetinriktad port och vidarebefordrar trafik till den interna nodens port som används av ditt program. Om du inte har någon belastningsutjämnare läser [du Konfigurera en Internet-vänd belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md).

Om du vill skapa en belastningsutjämnad regel måste du samla in följande information:

- Belastningsutjämnarens namn.
- Resursgruppen för belastningsutjämnaren och serviceinfrastrukturklustret.
- Extern port.
- Intern port.

## <a name="azure-cli"></a>Azure CLI
Det krävs bara ett enda kommando för att skapa en belastningsutjämnad regel med **Azure CLI**. Du behöver bara veta både namnet på belastningsutjämnaren och resursgruppen för att skapa en ny regel.

>[!NOTE]
>Om du behöver bestämma namnet på belastningsutjämnaren använder du det här kommandot för att snabbt få en lista över alla belastningsutjämnare och associerade resursgrupper.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI-kommandot har några parametrar som beskrivs i följande tabell:

| Parameter | Beskrivning |
| --------- | ----------- |
| `--backend-port`  | Porten som Service Fabric-programmet lyssnar på. |
| `--frontend-port` | Porten som belastningsutjämnaren exponerar för externa anslutningar. |
| `-lb-name` | Namnet på den belastningsutjämnare som ska ändras. |
| `-g`       | Resursgruppen som har både belastningsutjämnaren och service fabric-klustret. |
| `-n`       | Det önskade namnet på regeln. |


>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med Azure CLI finns i [Skapa en belastningsutjämnare med Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell är lite mer komplicerat än Azure CLI. Så här skapar du en regel:

1. Hämta belastningsutjämnaren från Azure.
2. Skapa en regel.
3. Lägg till regeln i belastningsutjämnaren.
4. Uppdatera belastningsutjämnaren.

>[!NOTE]
>Om du behöver bestämma namnet på belastningsutjämnaren använder du det här kommandot för att snabbt få en lista över alla belastningsutjämnare och associerade resursgrupper.
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

När `New-AzLoadBalancerRuleConfig` det gäller `-FrontendPort` kommandot representerar den port som belastningsutjämnaren exponerar för externa anslutningar och `-BackendPort` representerar porten som tjänstinfrastrukturappen lyssnar på.

>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med PowerShell finns i [Skapa en belastningsutjämnare med PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [nätverk i Service Fabric](service-fabric-patterns-networking.md).
