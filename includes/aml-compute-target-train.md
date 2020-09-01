---
title: inkludera fil
description: inkludera fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146749"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa**. När du till exempel ansluter en virtuell dator till din arbets yta kan du återanvända den för flera jobb.  För maskin inlärnings pipeliner använder du lämpligt [pipeline-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) för varje beräknings mål.

|Utbildnings &nbsp; mål|[Automatisk ML](../articles/machine-learning/concept-automated-ml.md) | [ML-pipelines](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| Ja & <br/>inställning av min parameter &nbsp; | ja | ja |
|[Azure Machine Learning-beräkningsinstans](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | Ja & <br/>inställning av min parameter | ja |  |
|[VIRTUELL fjärrdator](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | Ja & <br/>inställning av min parameter | ja | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| Ja (endast SDK lokalt läge) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | ja | &nbsp; |
