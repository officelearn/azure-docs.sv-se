---
title: Ansluta till Azure Kubernetes Service - Azure Database for PostgreSQL - Single Server
description: Lär dig mer om hur du ansluter Azure Kubernetes Service (AKS) med Azure Database for PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769888"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Ansluta Azure Kubernetes Service och Azure Database för PostgreSQL - Single Server

Azure Kubernetes Service (AKS) tillhandahåller ett hanterat Kubernetes-kluster som du kan använda i Azure. Nedan finns några alternativ att tänka på när du använder AKS och Azure Database för PostgreSQL tillsammans för att skapa ett program.


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

Med OSBA kan du skapa en Azure-databas för PostgreSQL-server och binda den till AKS-klustret med Kubernetes modersmål. Lär dig mer om hur du använder OSBA och Azure Database för PostgreSQL tillsammans på [OSBA GitHub-sidan](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Anslutningspoolning
En anslutningspooler minimerar kostnaden och tiden i samband med att nya anslutningar skapas och stängs till databasen. Poolen är en samling anslutningar som kan återanvändas. 

Det finns flera anslutningspoolers som du kan använda med PostgreSQL. En av dessa är [PgBouncer](https://pgbouncer.github.io/). I Microsoft Container Registry tillhandahåller vi en lätt containeriserad PgBouncer som kan användas i en sidovagn för att poola anslutningar från AKS till Azure Database för PostgreSQL. Besök [docker-hubbsidan](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) om du vill lära dig hur du kommer åt och använder den här bilden. 


## <a name="next-steps"></a>Nästa steg
-  [Skapa ett Azure Kubernetes-tjänstkluster](../aks/kubernetes-walkthrough.md)
