---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91640751"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa**. När du till exempel ansluter en virtuell dator till din arbets yta kan du återanvända den för flera jobb.  För maskin inlärnings pipeliner använder du lämpligt [pipeline-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) för varje beräknings mål.

|Utbildnings &nbsp; mål|[Automatisk ML](../articles/machine-learning/concept-automated-ml.md) | [ML-pipelines](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| Ja & <br/>inställning av min parameter &nbsp; | ja | ja |
|[Azure Machine Learning-beräkningsinstans](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | Ja (via SDK) | ja |  |
|[VIRTUELL fjärrdator](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | Ja & <br/>inställning av min parameter | ja | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| Ja (endast SDK lokalt läge) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | ja | &nbsp; |
