---
title: Ansluta till Azure Kubernetes service – Azure Database for PostgreSQL-enskild server
description: Lär dig mer om att ansluta Azure Kubernetes service (AKS) med Azure Database for PostgreSQL-enskild server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74769888"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Ansluta Azure Kubernetes-tjänsten och Azure Database for PostgreSQL-enskild server

Azure Kubernetes service (AKS) tillhandahåller ett hanterat Kubernetes-kluster som du kan använda i Azure. Nedan visas några alternativ att tänka på när du använder AKS och Azure Database for PostgreSQL tillsammans för att skapa ett program.


## <a name="accelerated-networking"></a>Snabbare nätverk
Använd accelererade nätverk-aktiverade underliggande virtuella datorer i ditt AKS-kluster. När accelererat nätverk har Aktiver ATS på en virtuell dator finns det kortare svars tider, färre Darr och minskad processor användning på den virtuella datorn. Lär dig mer om hur påskyndat nätverk fungerar, vilka OS-versioner som stöds och vilka virtuella dator instanser som stöds för [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Från november 2018 har AKS stöd för accelererat nätverk på de virtuella dator instanser som stöds. Accelererat nätverk är aktiverat som standard på nya AKS-kluster som använder de virtuella datorerna.

Du kan kontrol lera om ditt AKS-kluster har accelererat nätverk:
1. Gå till Azure Portal och välj ditt AKS-kluster.
2. Välj fliken Egenskaper.
3. Kopiera namnet på **infrastruktur resurs gruppen**.
4. Använd Portal Sök fältet för att hitta och öppna infrastruktur resurs gruppen.
5. Välj en virtuell dator i resurs gruppen.
6. Gå till fliken **nätverk** på den virtuella datorn.
7. Bekräfta om det **accelererade nätverket** är aktiverat.

Eller via Azure CLI med följande två kommandon:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Utdata blir den genererade resurs gruppen som AKS skapar med nätverks gränssnittet. Ta namnet "nodeResourceGroup" och Använd det i nästa kommando. **EnableAcceleratedNetworking** är antingen sant eller falskt:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Öppna Service Broker för Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) gör att du kan etablera Azure-tjänster direkt från Kubernetes eller Cloud Foundry. Det är en [öppen service BROKER API](https://www.openservicebrokerapi.org/) -implementering för Azure.

Med OSBA kan du skapa en Azure Database for PostgreSQL-Server och binda den till ditt AKS-kluster med Kubernetes inbyggda språk. Lär dig mer om hur du använder OSBA och Azure Database for PostgreSQL tillsammans på [sidan OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Anslutningspoolen
En anslutningspoolen minimerar kostnaden och tiden som är kopplad till att skapa och stänga nya anslutningar till databasen. Poolen är en samling anslutningar som kan återanvändas. 

Det finns flera anslutningspooler som du kan använda med PostgreSQL. En av dessa är [PgBouncer](https://pgbouncer.github.io/). I Microsoft Container Registry tillhandahåller vi en lätt behållar PgBouncer som kan användas i en sidvagn för att pool anslutningar från AKS till Azure Database for PostgreSQL. Besök [sidan Docker Hub](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) och lär dig hur du kommer åt och använder den här avbildningen. 


## <a name="next-steps"></a>Nästa steg
-  [Skapa ett Azure Kubernetes service-kluster](../aks/kubernetes-walkthrough.md)
