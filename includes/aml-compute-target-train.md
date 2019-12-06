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
ms.date: 12/04/2019
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875491"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa**. När du till exempel ansluter en virtuell dator till din arbets yta kan du återanvända den för flera jobb.  För maskin inlärnings pipeliner använder du lämpligt [pipeline-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) för varje beräknings mål.

|Utbildning &nbsp;mål|[Automatiserad ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML pipelines](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning designer](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Ja & <br/>fin parameter&nbsp;justering | ja | ja |
|[Fjärransluten virtuell dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | Ja & <br/>inställning av min parameter | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ja | &nbsp; |
