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
ms.date: 09/17/2020
ms.openlocfilehash: 87b1f4ab7b7091970d7bb76ae1e00b06549fb0b4
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96152713"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa.** När du till exempel har anslutit en virtuell dator till din arbets yta kan du återanvända den för flera jobb. För maskin inlärnings pipeliner använder du lämpligt [pipeline-steg](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) för varje beräknings mål.

Du kan använda någon av följande resurser för ett utbildnings mål för de flesta jobb. Alla resurser kan inte användas för automatisk maskin inlärning, maskin inlärnings pipeliner eller designer.

|Utbildnings &nbsp; mål|[Automatiserad maskininlärning](../articles/machine-learning/concept-automated-ml.md) | [Pipelines för maskininlärning](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Ja | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Ja | Ja | Ja |
|[Azure Machine Learning-beräkningsinstans](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ja (via SDK)  | Ja |  |
|[VIRTUELL fjärrdator](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Ja  | Ja | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ja (endast SDK lokalt läge) | Ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Ja | &nbsp; |
