---
title: Exempel och genomgångar för virtuella datorer för datavetenskap – Azure | Microsoft Docs
description: Exempel och genomgångar för virtuella datorer för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: 74d5893a377fb67dbec7b185525e74ac248deede
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902764"
---
# <a name="samples-on-data-science-virtual-machines"></a>Exempel på virtuella datorer för datavetenskap

Azure virtuella datorer för datavetenskap innehåller en omfattande uppsättning med exempelkod. Exempelkoden är i form av Jupyter-anteckningsböcker och skript i språk, till exempel Python och R. 
> [!NOTE]
> Läs mer om hur du kör Jupyter-anteckningsböcker på dina virtuella datorer för datavetenskap, den [åtkomst Jupyter](#access-jupyter) avsnittet.

## <a name="quick-reference-of-samples"></a>Snabbreferens exempel
| Exempel-kategori | Beskrivning | Platser |
| ------------- | ------------- | ------------- |
| R-språket  | Exempel i R beskrivs scenarier, till exempel hur du ansluter med datalager i Azure-molnet. De beskrivs också hur du jämför open source-R- och Microsoft R. Och de förklarar hur du operationalisera modeller på Microsoft R Server eller SQL Server. <br/> [R-språket](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python-språk  | Exemplen i Python förklara till exempel hur du ansluter med datalager i Azure-molnet och arbetar med Azure Machine Learning.  <br/> [Python-språk](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia-språket  | Exemplet i Julia som beskriver ritning och djup maskininlärning i Julia. Här förklaras också anropande C och Python från Julia. <br/> [Julia-språket](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Skapa machine learning och deep learning-modeller med Machine Learning. Distribuera modeller var som helst. Använd automatiserade maskininlärning och intelligent finjustering av hyperparametrar. Också använda modellhantering och distribuerad utbildning. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch-anteckningsböcker  | Exempel som använder PyTorch-baserade neurala nätverk för djupinlärning. Anteckningsböcker allt från nybörjare till avancerade scenarier.  <br/> [PyTorch-anteckningsböcker](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Exempel för olika neurala nätverk och tekniker som implementeras med hjälp av TensorFlow-framework. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Exempel som publicerats av Cognitive Toolkit-teamet hos Microsoft för djupinlärning.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Exempel som använder caffe2-baserade neurala nätverk för djupinlärning. Flera anteckningsböcker bekanta användare med caffe2 och hur du använder effektivt. Exempel: skapa förbearbetning och datamängd för avbildningar. De omfattar också regression och hur du använder förtränade modeller. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Python-baserade exempel som använder H2O för verkligt scenario problem. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML språk  | Exempel som använder funktioner i Spark MLLib toolkit via pySpark och MMLSpark--Microsoft Machine Learning för Apache Spark i Apache Spark 2.x.  <br/> [SparkML språk](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standard machine learning-exempel i XGBoost för scenarier som klassificerings- och regressionsmodeller. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Åtkomst Jupyter 

För att komma åt Jupyter, Välj den `Jupyter` -ikonen i menyn skrivbord eller ett program. Du kan också nå Jupyter på Linux-versioner av virtuella datorer för datavetenskap. Du kan komma åt via en fjärranslutning från en webbläsare genom att besöka `https://<Full Domain Name or IP Address of the DSVM>:8000` på Ubuntu.

Om du vill lägga till undantag och tillgängliggöra Jupyter komma åt via en webbläsare, finns i följande skärmbild.


![Aktivera Jupyter-undantag](./media/ubuntu-jupyter-exception.png)


Logga in med samma lösenord som din inloggning för virtuella datorer för datavetenskap.
<br/>

**Jupyter hem**
<br/>![Jupyter hem](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R-språket 
<br/>![R-exempel](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python-språk
<br/>![Python-exempel](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia-språket 
<br/>![Julia-exempel](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML-exempel](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch-exempel](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-exempel](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-exempel](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2-exempel](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O-exempel](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML-exempel](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost-exempel](./media/xgboost-samples.png)<br/>

