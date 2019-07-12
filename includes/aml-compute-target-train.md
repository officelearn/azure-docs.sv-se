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
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806039"
---
**Compute mål kan återanvändas från en utbildningsjobb till nästa**. När du kopplar en fjärransluten virtuell dator till din arbetsyta, kan du till exempel återanvända den för flera jobb.

|Utbildning &nbsp;mål| GPU-stöd |[Automatiserad ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML-pipelines](../articles/machine-learning/service/concept-ml-pipelines.md) | [Visuella gränssnittet](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| maybe | ja | &nbsp; | &nbsp; |
|[Azure Machine Learning-beräkning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ja | Ja & <br/>finjustering&nbsp;justering | ja | ja |
|[Fjärransluten virtuell dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ja | Ja & <br/>finjustering av hyperparametrar | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ja | &nbsp; |
