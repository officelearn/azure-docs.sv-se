---
title: Ansluta till Azure Kubernetes Service - Azure Database för MySQL
description: Lär dig mer om hur du ansluter Azure Kubernetes-tjänsten med Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537286"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Ansluta Azure Kubernetes-tjänsten och Azure-databasen för MySQL

Azure Kubernetes Service (AKS) tillhandahåller ett hanterat Kubernetes-kluster som du kan använda i Azure. Nedan följer några alternativ att tänka på när du använder AKS och Azure Database för MySQL tillsammans för att skapa ett program.


## <a name="accelerated-networking"></a>Snabbare nätverk
Använd accelererade nätverksaktiverade underliggande virtuella datorer i AKS-klustret. När accelererade nätverk är aktiverat på en virtuell dator finns det lägre svarstid, minskad jitter och minskad CPU-användning på den virtuella datorn. Läs mer om hur accelererade nätverk fungerar, os-versioner som stöds och vm-instanser som stöds för [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Från och med november 2018 stöder AKS accelererade nätverk för de VM-instanser som stöds. Accelererade nätverk är aktiverat som standard på nya AKS-kluster som använder dessa virtuella datorer.

Du kan bekräfta om AKS-klustret har påskyndat nätverken:
1. Gå till Azure-portalen och välj ditt AKS-kluster.
2. Välj fliken Egenskaper.
3. Kopiera namnet på **infrastrukturresursgruppen**.
4. Använd sökfältet i portalen för att hitta och öppna infrastrukturresursgruppen.
5. Välj en virtuell dator i resursgruppen.
6. Gå till fliken **Vm-nätverk.**
7. Bekräfta om **Accelererat nätverk** är aktiverat.

Eller via Azure CLI med följande två kommandon:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Utdata kommer att vara den genererade resursgruppen som AKS skapar som innehåller nätverksgränssnittet. Ta namnet "nodeResourceGroup" och använd det i nästa kommando. **EnableAcceleratedNetworking** kommer antingen att vara sant eller falskt:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) låter dig etablera Azure-tjänster direkt från Kubernetes eller Cloud Foundry. Det är en [API-implementering](https://www.openservicebrokerapi.org/) för Öppen tjänstmäklare för Azure.

Med OSBA kan du skapa en Azure Database för MySQL-server och binda den till AKS-klustret med Kubernetes modersmål. Lär dig mer om hur du använder OSBA och Azure Database for MySQL tillsammans på [OSBA GitHub-sidan](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Nästa steg
- [Skapa ett Azure Kubernetes-tjänstkluster](../aks/kubernetes-walkthrough.md)
- Lär dig hur du [installerar WordPress från ett Helm-diagram med OSBA och Azure Database for MySQL](../aks/integrate-azure.md)
