---
title: Machine Learning och AI med ONNX i Azure SQL Edge (för hands version)
description: Machine Learning i Azure SQL Edge (för hands version) stöder modeller i formatet Open neurala Network Exchange (ONNX). ONNX är ett öppet format som du kan använda för att Interchange modeller mellan olika ramverk och verktyg för maskin inlärning.
keywords: Distribuera SQL Edge
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 2db6c728ac35c6fc2f1fee1a602725371e448104
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368064"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Machine Learning och AI med ONNX i SQL Edge (för hands version)

Machine Learning i Azure SQL Edge (för hands version) stöder modeller i formatet [Open neurala Network Exchange (ONNX)](https://onnx.ai/) . ONNX är ett öppet format som du kan använda för att Interchange modeller mellan olika [ramverk och verktyg för maskin inlärning](https://onnx.ai/supported-tools).

## <a name="overview"></a>Översikt

För att kunna härleda maskin inlärnings modeller i Azure SQL Edge måste du först skaffa en modell. Detta kan vara en förtränad modell eller en anpassad modell som är utbildad med ditt ramverk av val. Azure SQL Edge stöder ONNX-formatet och du måste konvertera modellen till det här formatet. Det bör inte uppstå någon påverkan på modell precisionen och när du har ONNX-modellen kan du distribuera modellen i Azure SQL Edge och använda [en intern bedömning med predict T-SQL-funktionen](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Så här hämtar du en modell i ONNX-formatet:

- **Bygg tjänster för modeller**: tjänster, till exempel [funktionen för automatisk Machine Learning i Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) och [Azure Custom vision service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) stöd för att exportera den tränade modellen i ONNX-format.

- [**Konvertera och/eller exportera befintliga modeller**](https://github.com/onnx/tutorials#converting-to-onnx-format): flera utbildnings ramverk (t. ex. [PyTorch](https://pytorch.org/docs/stable/onnx.html), kedjer och Caffe2) har stöd för interna export funktioner till ONNX, vilket gör att du kan spara den tränade modellen till en specifik version av ONNX-formatet. För ramverk som inte stöder intern export finns det fristående ONNX-omvandlare som gör det möjligt att konvertera modeller som har tränats från olika ramverk för maskin inlärning till ONNX-formatet.

     **Ramverk som stöds**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit – lär dig](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    En fullständig lista över ramverk som stöds och exempel finns i [konvertera till ONNX-format](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Begränsningar

För närvarande stöds inte alla ONNX-modeller av Azure SQL Edge. Stödet är begränsat till modeller med **numeriska data typer**:

- [int och bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [verkligt och flyttal](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andra numeriska typer kan konverteras till typer som stöds med [Cast och Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Modellens indata ska vara strukturerade så att varje indata till modellen motsvarar en enskild kolumn i en tabell. Om du till exempel använder en Pandas-dataframe för att träna en modell ska varje Indatatyp vara en separat kolumn till modellen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Edge via Azure Portal](deploy-portal.md)
- [Distribuera en ONNX-modell på Azure SQL Edge (för hands version)](deploy-onnx.md)
