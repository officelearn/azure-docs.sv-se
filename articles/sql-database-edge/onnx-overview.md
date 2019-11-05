---
title: Machine Learning och AI med ONNX i Azure SQL Database Edge Preview | Microsoft Docs
description: Machine Learning i Azure SQL Database Edge-förhandsgranskning stöder modeller i formatet Open neurala Network Exchange (ONNX). ONNX är ett öppet format som du kan använda för att Interchange modeller mellan olika ramverk och verktyg för maskin inlärning.
keywords: Distribuera SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514022"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning och AI med ONNX i SQL Database Edge Preview

Machine Learning i Azure SQL Database Edge-förhandsgranskning stöder modeller i formatet [Open neurala Network Exchange (ONNX)](https://onnx.ai/) . ONNX är ett öppet format som du kan använda för att Interchange modeller mellan olika [ramverk och verktyg för maskin inlärning](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Paket som stöds

Med ONNXMLTools kan du konvertera modeller från olika Machine Learning Tool-paket till en ONNX-modell. För närvarande stöds följande verktyg för numeriska data typer och indata från en kolumn:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (experimentell)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Du kan hämta en modell i ONNX-format på flera sätt:

- [ONNX-modell Zoo](https://github.com/onnx/models): innehåller flera förtränade ONNX-modeller för olika typer av aktiviteter. Du kan hämta och använda versioner som stöds av Windows Machine Learning.

- [Intern export från ramverk för Machine Learning-utbildning](https://onnx.ai/supported-tools): flera utbildnings ramverk har stöd för interna export funktioner till ONNX, vilket gör att du kan spara din utbildade modell till en specifik version av ONNX-formatet. Till exempel kedjer, Caffee2 och PyTorch. Dessutom tillhandahåller tjänster som [Azure Machine Learning service](https://azure.microsoft.com/services/machine-learning-service/) och [Azure Custom vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) även intern ONNX-export.

  - Information om hur du tränar och exporterar en ONNX-modell i molnet med hjälp av Custom Vision finns i [Självstudier: använda en ONNX-modell från Custom vision med Windows ml (för hands version)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Konvertera befintliga modeller med WinMLTools: med](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools)det här python-paketet kan modeller konverteras från flera utbildnings Ramverks format till ONNX. Du kan ange vilken version av ONNX du vill konvertera din modell till, beroende på vilka versioner av Windows som dina program är mål för. Om du inte är bekant med python kan du konvertera dina modeller med hjälp av [Windows Machine Learning UI-baserade instrument panelen](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) .

> [!IMPORTANT]
> Det är inte alla ONNX-versioner som stöds av Azure SQL Database Edge. Det finns för närvarande stöd för att förutsäga numeriska data typer genom ONNX-modellen.

När du har en ONNX-modell kan du distribuera modellen i Azure SQL Database Edge. Du kan sedan använda [en intern poängsättning med predict T-SQL-funktionen](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Begränsningar

För närvarande är stödet begränsat till modeller med **numeriska data typer**:

- [int och bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [verkligt och flyttal](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andra numeriska typer kan konverteras till typer som stöds med CAST and CONVERT [Cast and Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Modellens indata ska vara strukturerade så att varje indata till modellen motsvarar en enskild kolumn i en tabell. Om du till exempel använder en Pandas-dataframe för att träna en modell ska varje Indatatyp vara en separat kolumn till modellen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Database Edge via Azure Portal](deploy-portal.md)
- [Distribuera en ONNX-modell på Azure SQL Database Edge-förhandsgranskning](deploy-onnx.md)