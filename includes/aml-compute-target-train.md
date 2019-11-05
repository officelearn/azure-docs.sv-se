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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489581"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa**. När du till exempel ansluter en virtuell dator till din arbets yta kan du återanvända den för flera jobb.

|Utbildning &nbsp;mål| GPU-stöd |[Automatiserad ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML pipelines](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning designer](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| kanske | ja | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings instans](../articles/machine-learning/service/concept-compute-instance.md)| ja | | ja |  |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ja | Ja & <br/>fin parameter&nbsp;justering | ja | ja |
|[VIRTUELL fjärrdator](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ja | Ja & <br/>inställning av min parameter | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ja | &nbsp; |
