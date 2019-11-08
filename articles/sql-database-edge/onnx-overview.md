---
title: Machine Learning och AI med ONNX i Azure SQL Database Edge Preview | Microsoft Docs
description: Machine Learning i Azure SQL Database Edge-förhandsgranskning stöder modeller i formatet Open neurala Network Exchange (ONNX). ONNX är ett öppet format som du kan använda för att Interchange modeller mellan olika ramverk och verktyg för maskin inlärning.
keywords: Distribuera SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822838"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning och AI med ONNX i SQL Database Edge Preview

Machine Learning i Azure SQL Database Edge-förhandsgranskning stöder modeller i formatet [Open neurala Network Exchange (ONNX)](https://onnx.ai/) . ONNX är ett öppet format som du kan använda för att Interchange modeller mellan olika [ramverk och verktyg för maskin inlärning](https://onnx.ai/supported-tools).

## <a name="overview"></a>Översikt

För att kunna härleda maskin inlärnings modeller i Azure SQL Database Edge måste du först skaffa en modell. Detta kan vara en förtränad modell eller en anpassad modell som är utbildad med ditt ramverk av val. Azure SQL Database Edge stöder ONNX-formatet och du måste konvertera modellen till det här formatet. Det bör inte uppstå någon påverkan på modell precisionen och när du har ONNX-modellen kan du distribuera modellen i Azure SQL Database Edge och använda [en intern bedömning med predict T-SQL-funktionen](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Du kan hämta en modell i ONNX-format på flera sätt:

- [ONNX-modell Zoo](https://github.com/onnx/models): innehåller många förtränade ONNX-modeller för olika typer av uppgifter som kan hämtas och är redo att användas.

- [Intern export från ml utbildnings ramverk](https://onnx.ai/supported-tools): flera utbildnings ramverk har stöd för interna export funktioner till ONNX, vilket gör att du kan spara din utbildade modell till en specifik version av ONNX-formatet, inklusive [PyTorch](https://pytorch.org/docs/stable/onnx.html), kedjor och Caffe2. Dessutom tillhandahåller modell byggnads tjänster, till exempel [funktionen automatiserad Machine Learning i Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) och [Azure Custom vision service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) ONNX export.

- [Konvertera befintliga modeller](https://github.com/onnx/tutorials#converting-to-onnx-format): för ramverk som inte stöder intern export finns det fristående paket för konvertering av modeller till ONNX-format. Exempel och självstudier finns i [konvertera till ONNX-format](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Ramverk som stöds

Med ONNX-konverterare kan du konvertera modeller som har tränats från olika ramverk för maskin inlärning till ONNX-formatet. Populära konverterare är: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

En fullständig lista över ramverk som stöds finns i [konvertera till ONNX-format](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Begränsningar

För närvarande stöds inte alla ONNX-modeller av Azure SQL Database Edge. Stödet är begränsat till modeller med **numeriska data typer**:

- [int och bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [verkligt och flyttal](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andra numeriska typer kan konverteras till typer som stöds med [Cast och Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Modellens indata ska vara strukturerade så att varje indata till modellen motsvarar en enskild kolumn i en tabell. Om du till exempel använder en Pandas-dataframe för att träna en modell ska varje Indatatyp vara en separat kolumn till modellen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Database Edge via Azure Portal](deploy-portal.md)
- [Distribuera en ONNX-modell på Azure SQL Database Edge-förhandsgranskning](deploy-onnx.md)
