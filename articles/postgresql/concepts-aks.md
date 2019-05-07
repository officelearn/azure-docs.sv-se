---
title: Ansluta Azure Kubernetes Service (AKS) med Azure Database för PostgreSQL – enskild Server
description: Läs om hur du ansluter Azure Kubernetes Service med Azure Database för PostgreSQL – enskild Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: a98d9b89db0406d67d1b067c3e53eb5c3dae7957
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068947"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Ansluta Azure Kubernetes Service och Azure Database för PostgreSQL – enskild Server

Azure Kubernetes Service (AKS) innehåller ett hanterat Kubernetes-kluster som du kan använda i Azure. Nedan visas några alternativ att överväga med AKS och Azure Database för PostgreSQL tillsammans för att skapa ett program.


## <a name="accelerated-networking"></a>Snabbare nätverk
Använda accelererat nätverk aktiverat underliggande virtuella datorer i AKS-klustret. Om accelererat nätverk är aktiverat på en virtuell dator, är det kortare svarstider, reducerat jitter och minskad CPU-belastning på den virtuella datorn. Mer information om hur accelererat nätverk fungerar, de OS-versionerna som stöds och stöd för VM-instanser till [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Från November 2018 AKS har stöd för accelererat nätverk på dessa VM-instanser som stöds. Accelererat nätverk är aktiverat som standard på nya AKS-kluster som använder dessa virtuella datorer.

Du kan bekräfta om AKS-klustret har accelererat nätverk:
1. Gå till Azure-portalen och välj AKS-klustret.
2. Välj fliken Egenskaper.
3. Kopiera namnet på den **Infrastrukturresursgrupp**.
4. Använda portalen sökfältet för att hitta och öppna resursgruppen infrastruktur.
5. Välj en virtuell dator i den resursgruppen.
6. Gå till Virtuellt datorns **nätverk** fliken.
7. Bekräfta om **Accelerated networking** 'Aktiveras ”.

Eller via Azure CLI med hjälp av följande två kommandon:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Följande utdata returneras genererade resursgruppen att AKS skapar som innehåller nätverksgränssnittet. Ta med namnet ”nodeResourceGroup” och använda den i nästa kommando. **EnableAcceleratedNetworking** ska antingen vara true eller false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Öppna Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) kan du etablera Azure-tjänster direkt från Kubernetes eller Cloud Foundry. Det är en [Open Service Broker API](https://www.openservicebrokerapi.org/) implementering för Azure.

Du kan skapa en Azure Database for PostgreSQL-server och koppla det till AKS-klustret med hjälp av Kubernetes' modersmål med OSBA. Lär dig mer om hur du använder OSBA och Azure Database för PostgreSQL tillsammans på den [OSBA GitHub-sidan](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Anslutningspooler
En anslutningspooler minimerar kostnaderna och tid som är associerade med att skapa och stänga nya anslutningar till databasen. Poolen är en samling av anslutningar som kan återanvändas. 

Det finns flera anslutning poolers som du kan använda med PostgreSQL. En av dessa är [PgBouncer](https://pgbouncer.github.io/). Microsoft Container Registry innehåller en enkel behållare PgBouncer som kan användas i en sidovagn till poolanslutningar från AKS till Azure Database för PostgreSQL. Gå till den [docker hub-sidan](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) att lära dig hur du använder den här avbildningen. 


## <a name="next-steps"></a>Nästa steg
-  [Skapa ett Azure Kubernetes Service-kluster](../aks/kubernetes-walkthrough.md)
