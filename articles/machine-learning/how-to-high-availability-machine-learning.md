---
title: Öka återhämtningen
titleSuffix: Azure Machine Learning
description: Lär dig hur du gör dina Azure Machine Learning-relaterade resurser mer elastiska för drift störningar genom att använda en konfiguration med hög tillgänglighet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100171"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Öka Azure Machine Learningens återhämtnings förmåga

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du gör dina Azure Machine Learning-relaterade resurser mer elastiska med hjälp av konfigurationer med hög tillgänglighet. De Azure-tjänster som Azure Machine Learning är beroende av kan konfigureras för hög tillgänglighet. Den här artikeln innehåller information om vilka tjänster som kan konfigureras för hög tillgänglighet och länkar till information om hur du konfigurerar resurserna.

> [!NOTE]
> Azure Machine Learning kan inte erbjuda ett alternativ för haveri beredskap.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Förstå Azure-tjänster för Azure Machine Learning

Azure Machine Learning är beroende av flera olika Azure-tjänster och har flera lager. En del av dem är etablerade i din (kund) prenumeration. Du ansvarar för konfigurationen av hög tillgänglighet för de här tjänsterna. Vissa skapas i en Microsoft-prenumeration och hanteras av Microsoft.

* **Azure Machine Learning infrastruktur**: Microsoft-hanterad miljö för Azure Machine Learning-arbetsyta.

* **Associerade resurser**: resurser etablerade i prenumerationen under Azure Machine Learning skapa arbets yta. De omfattar Azure Storage, Azure Key Vault, Azure Container Registry (ACR) och App Insights. Du ansvarar för inställningarna för hög tillgänglighet för dessa resurser.
  * Standard lagringen har data, till exempel modell, tränings loggar och data uppsättningar.
  * Key Vault har autentiseringsuppgifter för lagring, ACR, data lager.
  * ACR har Docker-avbildning för Training-och inferencing-miljö.
  * App Insights är för att övervaka Azure Machine Learning.

* **Beräknings resurser**: resurser som du skapar efter distribution av arbets ytan. Du kan till exempel skapa en beräknings instans eller ett beräknings kluster för att träna en Machine Learning-modell.
  * Beräknings instans och beräknings kluster: Microsoft-hanterad modell utvecklings miljö.
  * Andra resurser: de är dator resurser som kan kopplas till Azure Machine Learning, till exempel Azure Kubernetes service (AKS), Azure Databricks, Azure Container Instances (ACI) och HDInsight. Du ansvarar för inställningen för hög tillgänglighet.

* **Ytterligare data lager**: Azure Machine Learning kan montera ytterligare data lager, till exempel Azure Storage, Azure Data Lake Storage och Azure SQL Database för tränings data.  De finns i din prenumeration och du ansvarar för inställningen för hög tillgänglighet.

I följande tabell visas vilka tjänster som hanteras av Microsoft, som hanteras av dig och som är hög tillgängliga som standard:

| Tjänst | Hanteras av | HA som standard |
| ----- | ----- | ----- |
| **Azure Machine Learning infrastruktur** | Microsoft | |
| **Associerade resurser** |
| Azure Storage | Du | |
| Azure Key Vault | Du | ✓ |
| Azure Container Registry | Du | |
| Application Insights | Du | Ej tillämpligt |
| **Beräknings resurser** |
| Beräknings instans | Microsoft |  |
| Beräknings kluster | Microsoft |  |
| Andra resurser, till exempel Azure Kubernetes service, <br>Azure Databricks, Azure Container instance, Azure HDInsight | Du |  |
| **Ytterligare data lager** , till exempel Azure Storage, Azure SQL Database,<br> Azure Database for PostgreSQL Azure Database for MySQL, <br>Azure Databricks fil system | Du | |

Använd informationen i resten av det här dokumentet för att lära dig de åtgärder som krävs för att göra var och en av dessa tjänster hög tillgängliga.

## <a name="associated-resources"></a>Associerade resurser

> [!IMPORTANT]
> Azure Machine Learning stöder inte standard lagrings konto redundans med Geo-redundant lagring (GRS) eller Geo-redundant lagring (GZRS) eller Geo-redundant lagring med Läs behörighet (RA-GRS) eller Läs åtkomst till geo-Zone-redundant lagring (RA-GZRS).

Se till att inställningen hög tillgänglighet för varje resurs med informationen nedan.

* **Azure Storage**: om du vill konfigurera inställningar för hög tillgänglighet, se [Azure Storage redundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: den tillhandahåller standard tjänsten för hög tillgänglighet och ingen användar åtgärd krävs.  Se [Azure Key Vault tillgänglighet och redundans](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure Container Registry**: Välj Premium-SKU för geo-replikering. Se [geo-replikering i Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: den ger inte inställningen för hög tillgänglighet. Du kan justera data lagrings period och information i [data insamling, kvarhållning och lagring i Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Beräknings resurser

Se till att inställningen hög tillgänglighet för varje resurs med följande dokumentation.

* **Azure Kubernetes-tjänst**: se [metod tips för verksamhets kontinuitet och haveri beredskap i Azure KUBERNETES service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) och [skapa ett Azure KUBERNETES service-kluster (AKS) som använder tillgänglighets zoner](https://docs.microsoft.com/azure/aks/availability-zones). Om AKS-klustret skapades av Azure Machine Learning (med Studio, SDK eller ML CLI) stöds inte hög tillgänglighet mellan regioner.
* **Azure Databricks**: se [regional haveri beredskap för Azure Databricks-kluster](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Azure Container instance**: ACI Orchestrator är ansvarig för redundans. Se [Azure Container instances-och behållar dirigeringar](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure HDInsight**: se [tjänster med hög tillgänglighet som stöds av Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Ytterligare data lager

Se till att inställningen hög tillgänglighet för varje resurs med följande dokumentation.

* **Azure Blob container/Azure File Share/Azure Data Lake Gen2**: samma som standard lagring.
* **Azure Data Lake gen1**: se[vägledning om haveri beredskap för data i Azure Data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Azure SQL Database**: se [hög tillgänglighet och Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure Database for PostgreSQL**: se [koncept med hög tillgänglighet i Azure Database for PostgreSQL-enskild server](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database for MySQL**: se [förstå affärs kontinuitet i Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Databricks-fil system**: se [regional haveri beredskap för Azure Databricks kluster](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Om du anger din egen nyckel (kundhanterad nyckel) för att distribuera Azure Machine Learning arbets ytan, tillhandahålls Cosmos DB också i din prenumeration. I så fall ansvarar du för dess hög tillgänglighet. Se [hög tillgänglighet med Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera Azure Machine Learning med associerade resurser med dina inställningar för hög tillgänglighet, använder du en [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).