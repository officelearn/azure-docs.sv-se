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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752950"
---
|Utbildning &nbsp;mål| GPU-stöd |[Automatiserad ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML-pipelines](../articles/machine-learning/service/concept-ml-pipelines.md) | [Visuella gränssnittet](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| maybe | ja | &nbsp; | &nbsp; |
|[Azure Machine Learning-beräkning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ja | Ja & <br/>finjustering&nbsp;justering | ja | ja |
|[Fjärransluten virtuell dator](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ja | Ja & <br/>finjustering av hyperparametrar | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ja | &nbsp; |

**Alla beräkningsresurser mål kan återanvändas för flera upplärningsjobb**. När du kopplar en fjärransluten virtuell dator till din arbetsyta, kan du till exempel återanvända den för flera jobb.