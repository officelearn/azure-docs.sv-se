---
title: Machine learning och AI med ONNX i Azure SQL Database Edge Preview | Microsoft-dokument
description: Machine learning i Azure SQL Database Edge Preview stöder modeller i ONNX-formatet (Open Neural Network Exchange). ONNX är ett öppet format som du kan använda för att utbyta modeller mellan olika ramverk för maskininlärning och verktyg.
keywords: distribuera sql-databaskant
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366267"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Maskininlärning och AI med ONNX i förhandsversionen av SQL Database Edge

Machine learning i Azure SQL Database Edge Preview stöder modeller i [ONNX-formatet (Open Neural Network Exchange).](https://onnx.ai/) ONNX är ett öppet format som du kan använda för att utbyta modeller mellan olika [ramverk för maskininlärning och verktyg](https://onnx.ai/supported-tools).

## <a name="overview"></a>Översikt

Om du vill dra slutsatsen att machine learning-modeller i Azure SQL Database Edge måste du först skaffa en modell. Detta kan vara en förtränad modell eller en anpassad modell som tränas med ditt ramverk. Azure SQL Database Edge stöder ONNX-formatet och du måste konvertera modellen till det här formatet. Det bör inte påverka modellens noggrannhet, och när du har ONNX-modellen kan du distribuera modellen i Azure SQL Database Edge och använda [inbyggd bedömning med funktionen PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Skaffa ONNX-modeller

Så här hämtar du en modell i ONNX-format:

- **Model Building Services**: Tjänster som den [automatiska machine learning-funktionen i Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) och Azure Custom Vision [Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) stöder direkt export av den tränade modellen i ONNX-format.

- [**Konvertera och/eller exportera befintliga modeller**](https://github.com/onnx/tutorials#converting-to-onnx-format): Flera utbildningsramverk (t.ex. [PyTorch](https://pytorch.org/docs/stable/onnx.html) För ramverk som inte stöder inbyggd export finns det fristående ONNX Converter-paket som gör att du kan konvertera modeller som tränas från olika ramverk för maskininlärning till ONNX-formatet.

     **Ramverk som stöds**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow (TRorflöde)](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML (Kärn)](https://github.com/onnx/onnxmltools)
    
    En fullständig lista över ramverk och exempel som stöds finns i [Konvertera till ONNX-format](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Begränsningar

För närvarande stöds inte alla ONNX-modeller av Azure SQL Database Edge. Stödet är begränsat till modeller med **numeriska datatyper:**

- [int och bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [verkliga och flyta](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andra numeriska typer kan konverteras till typer som stöds med hjälp av [CAST och CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Modellingångarna bör struktureras så att varje indata till modellen motsvarar en enda kolumn i en tabell. Om du till exempel använder en pandas-dataram för att träna en modell, ska varje indata vara en separat kolumn till modellen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Database Edge via Azure-portalen](deploy-portal.md)
- [Distribuera en ONNX-modell i förhandsversionen av Azure SQL Database Edge](deploy-onnx.md)
