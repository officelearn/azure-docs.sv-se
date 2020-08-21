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
ms.date: 12/04/2019
ms.openlocfilehash: 6ddff84de6b8ffd5bc8f7c7dcaa7cb4df3d71f81
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703495"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa**. När du till exempel ansluter en virtuell dator till din arbets yta kan du återanvända den för flera jobb.  För maskin inlärnings pipeliner använder du lämpligt [pipeline-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) för varje beräknings mål.

|Utbildnings &nbsp; mål|[Automatisk ML](../articles/machine-learning/concept-automated-ml.md) | [ML-pipelines](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ja & <br/>inställning av min parameter &nbsp; | ja | ja |
|[Azure Machine Learning-beräkningsinstans](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | Ja & <br/>inställning av min parameter | ja |  |
|[VIRTUELL fjärrdator](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ja & <br/>inställning av min parameter | ja | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Ja (endast SDK lokalt läge) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ja | &nbsp; |
