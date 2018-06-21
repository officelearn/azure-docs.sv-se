---
title: 'Migrera uppdaterade Azure Batch AI-API: et | Microsoft Docs'
description: Så här uppdaterar du Azure Batch AI-kod och skript för att använda arbetsytan och experimentera resurser
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: c5e4c1569464d2e204edf13fe7534d80780524e8
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294968"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrera uppdaterade Batch AI-API: et

Viktiga ändringar och nya funktioner har införts i Batch AI REST API version 2018-05-01 och relaterade Batch AI SDK: er och verktyg.

Om du har använt en tidigare version av Batch AI-API, förklarar den här artikeln hur du ändrar kod och skript för att fungera med den nya API. 

## <a name="whats-changing"></a>Vad ändra?

Som svar på kundfeedback ska vi ta bort begränsningar för antalet jobb och gör det lättare att hantera Batch AI-resurser. Den nya API introducerar två nya resurser *arbetsytan* och *experimentera*. Samla in relaterade utbildning jobb under ett experiment och ordna alla relaterade Batch AI resurser (kluster, filservrar, experiment, jobb) i en arbetsyta.  

* **Arbetsytan** -en samlingar av alla typer av Batch AI-resurser. Kluster- och filservrar finns i en arbetsyta. Arbetsytor vanligtvis separata arbete som hör till olika grupper eller projekt. Du kan till exempel ha en utvecklings- och en test-arbetsyta. Du behöver antagligen bara ett begränsat antal arbetsytor per prenumeration. 

* **Experiment** – en samling relaterade jobb som kan efterfrågas och hanteras tillsammans. Till exempel använda ett experiment för att gruppera alla jobb som utförs som en del av en hyper-parametern prestandajustering Svep. 

Följande bild visar ett exempel på resursen hierarki. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Övervaka och hantera befintliga resurser
I varje resursgrupp som du redan skapat Batch AI-kluster, jobb och filservrar, AI Batch-tjänsten ska skapa en arbetsyta med namnet `migrated-<region>` (till exempel `migrated-eastus`) och ett experiment med namnet `migrated`. Om du vill komma åt tidigare skapade jobb, kluster eller filservrar, måste du använda arbetsytan migrerade och experiment. 

### <a name="portal"></a>Portalen 
För att komma åt tidigare skapade jobb, kluster eller filservrar med hjälp av portalen först välja den `migrated-<region>` arbetsytan. När du har valt arbetsytan utföra åtgärder som att ändra storlek på eller ta bort ett kluster och visa jobbets status och utdata. 

### <a name="sdks"></a>SDK:er 
För att komma åt jobb, kluster eller filservrar som tidigare har skapats via Batch AI-SDK: er, ange namnet på arbetsytan och experimentera med parametrar. 

Om du använder Python SDK visas relevanta ändringar i följande exempel. Ändringar har liknande andra Batch AI SDK: erna. 


#### <a name="get-old-cluster"></a>Hämta gamla klustret 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Ta bort gamla klustret 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Hämta gamla filserver 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Ta bort gamla filserver  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Hämta gamla jobb 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Ta bort gamla jobb

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Använd när du använder Azure CLI åtkomst som tidigare har skapat jobb, kluster eller filservrar i `-w` och `-e` parametrar för att ange arbetsytan och experimentera namn. 


#### <a name="get-old-cluster"></a>Hämta gamla klustret

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Ta bort gamla klustret 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Hämta gamla filserver

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Ta bort gamla filserver 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Hämta gamla jobb

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Ta bort gamla jobb 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Skapa Batch AI-resurser 
 
Om du använder en befintlig Batch AI SDK kan du fortsätta att använda den för att skapa Batch AI-resurser (jobb, kluster eller filservrar) utan att göra kodändringar. Men när du har uppgraderat till nya SDK behöver du göra följande ändringar.
 
### <a name="create-workspace"></a>Skapa arbetsyta 
Beroende på ditt scenario kan du skapa en arbetsyta manuellt enstaka via portalen eller CLI. Om du använder CLI kan du skapa en arbetsyta med följande kommando: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Skapa experiment 


Beroende på ditt scenario kan du skapa ett experiment manuellt enstaka via portalen eller CLI. Om du använder CLI, skapa ett experiment med följande kommando: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Skapa kluster, filservrar och jobb
 
Om du använder portalen för att skapa jobb, kluster eller filservrar, vägleder portalen dig under Skapa erfarenhet att ange namnet på arbetsytan och experimentera med parametrar.

Ange parametern arbetsytan namn för att skapa jobb, kluster eller filservrar via den uppdaterade SDK. Om du använder Python SDK visas relevanta ändringar i följande exempel. Ersätt *workspace_name* och *experiment_name* med arbetsytan och experiment som du skapade tidigare. Ändringar har liknande andra Batch AI SDK: erna. 


#### <a name="create-cluster"></a>Skapa kluster 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Skapa filserver 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Skapa jobb 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Nästa steg

* Se Batch AI API-referens: [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), och [REST](/rest/api/batchai)