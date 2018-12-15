---
title: Migrera till uppdaterade Azure Batch AI-API | Microsoft Docs
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
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407788"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrera till den uppdaterade Batch AI-API

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Viktiga ändringar och nya funktioner har införts i Batch AI REST API-versionen 2018-05-01 och relaterade Batch AI SDK: er och verktyg.

Om du har använt en tidigare version av Batch AI-API kan förklarar den här artikeln hur du ändrar din kod och skript för att arbeta med den nya API: et. 

## <a name="whats-changing"></a>Vad ändras?

Vi ska ta bort gränser för antalet jobb och gör det enklare att hantera Batch AI-resurser som svar på kundfeedback. Den nya API: et introducerar två nya resurser, *arbetsytan* och *experimentera*. Samla in relaterade upplärningsjobb under ett experiment och organisera alla relaterade Batch AI resurser (kluster, filservrar, experiment, jobb) under en arbetsyta.  

* **Arbetsytan** – en samlingar av alla typer av Batch AI-resurser. Kluster- och filservrar finns i en arbetsyta. Arbetsytor vanligtvis separata arbete som hör till olika grupper eller projekt. Du kan till exempel ha en utvecklings- och en test-arbetsyta. Du behöver förmodligen bara ett begränsat antal arbetsytor per prenumeration. 

* **Experiment** – en uppsättning relaterade jobb som kan efterfrågas och hanteras tillsammans. Till exempel använda ett experiment för att gruppera alla jobb som utförs som en del av en hyper-justering parameterrensning. 

Följande bild visar ett exempelhierarki för resursen. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Övervaka och hantera befintliga resurser
I varje resursgrupp där du redan skapat Batch AI-kluster, jobb och filservrar, Batch AI-tjänsten skapar en arbetsyta med namnet `migrated-<region>` (till exempel `migrated-eastus`) och ett experiment med namnet `migrated`. För att komma åt den tidigare skapade jobb, kluster eller filservrar, måste du använda arbetsytan migrerade och experimentera. 

### <a name="portal"></a>Portalen 
För att komma åt tidigare skapade jobb, kluster eller filservrar med hjälp av portalen, väljer du först den `migrated-<region>` arbetsyta. När du har valt på arbetsytan, utföra åtgärder som ändrar storlek på eller tar bort ett kluster och visa jobbets status och utdata. 

### <a name="sdks"></a>SDK:er 
Ange arbetsytans namn om du vill komma åt jobb, kluster eller filservrar som tidigare har skapats via Batch AI-SDK: er, och experimentera namnparametrarna. 

Om du använder Python SDK visas relevanta ändringar i följande exempel. Ändringar liknar varandra i de andra Batch AI-SDK: er. 


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
 
När du använder Azure CLI för att åtkomst som skapats tidigare jobb, kluster eller filservrar, använda den `-w` och `-e` parametrar för att ange arbetsytan och experimentera namn. 


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
 
Om du använder en av de befintliga SDK: er för Batch AI kan du fortsätta att använda den för att skapa Batch AI-resurser (jobb, kluster eller filservrar) utan att göra ändringar i koden. När du har uppgraderat till ny SDK måste du dock göra följande ändringar används.
 
### <a name="create-workspace"></a>Skapa arbetsyta 
Beroende på ditt scenario kan du skapa en arbetsyta manuellt enstaka via portalen eller CLI. Om du använder CLI, skapar du en arbetsyta med hjälp av följande kommando: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Skapa experiment 


Beroende på ditt scenario kan du skapa ett experiment manuellt enstaka via portalen eller CLI. Om du använder CLI kan du skapa ett experiment med följande kommando: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Skapa kluster, filservrar och jobb
 
Om du använder portalen för att skapa jobb, kluster eller filservrar, vägleder portalen dig under skapandet av att ange arbetsytans namn och experimentera namnparametrarna.

För att skapa jobb, kluster eller filservrar via den uppdaterade SDK kan du ange parametern arbetsytan namn. Om du använder Python SDK visas relevanta ändringar i följande exempel. Ersätt *workspace_name* och *experiment_name* med den arbetsytan och experiment som du skapade tidigare. Ändringar liknar varandra i de andra Batch AI-SDK: er. 


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

* Se Batch AI-API-referens: [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), och [REST](/rest/api/batchai)