---
title: Ansluta Azure Kubernetes Service (AKS) med Azure Database for MySQL
description: Läs om hur du ansluter Azure Kubernetes Service med Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: 624689fd6b9d8f364b0caf7e96b79b2773ce6171
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538182"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Ansluta Azure Kubernetes Service och Azure Database for MySQL

Azure Kubernetes Service (AKS) innehåller ett hanterat Kubernetes-kluster som du kan använda i Azure. Nedan visas några alternativ att överväga med AKS och Azure Database för MySQL tillsammans för att skapa ett program.


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


## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Öppna Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) kan du etablera Azure-tjänster direkt från Kubernetes eller Cloud Foundry. Det är en [Open Service Broker API](https://www.openservicebrokerapi.org/) implementering för Azure.

Du kan skapa en Azure Database for MySQL-server och koppla det till AKS-klustret med hjälp av Kubernetes' modersmål med OSBA. Lär dig mer om hur du använder OSBA och Azure Database för MySQL tillsammans på den [OSBA GitHub-sidan](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Nästa steg
- [Skapa ett Azure Kubernetes Service-kluster](../aks/kubernetes-walkthrough.md)
- Lär dig hur du [installera WordPress från ett Helm-diagram med OSBA och Azure Database for MySQL](../aks/integrate-azure.md)
