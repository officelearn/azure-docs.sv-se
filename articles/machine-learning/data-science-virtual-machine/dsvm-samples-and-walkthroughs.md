---
title: Exempel och genomgångar för den virtuella datorn för datavetenskap – Azure | Microsoft Docs
description: Exempel och genomgångar för den virtuella datorn för datavetenskap.
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
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392576"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Exempel på de virtuella datorerna för datavetenskap (DSVM)

Dsvm innehåller en omfattande uppsättning exempelkoden både i form av Jupyter Notebooks samt skript på språk som Python och R.    
> [!NOTE]
> Referera till [åtkomst Jupyter](#access-jupyter) mer information om hur du kör Jupyter Notebooks på din DSVM.

## <a name="quick-reference-of-samples"></a>Snabbreferens exempel
| Exempel-kategori | Beskrivning | Platser |
| ------------- | ------------- | ------------- |
| **R** språk  | Exemplen i **R** förklarar scenarier som ansluter med Azure-molndata lagras, jämföra Open Source R och Microsoft R & operationalisera modeller på Microsoft R Server eller SQL Server. <br/> [Skärmbild](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** språk  | Exemplen i **Python** förklarar scenarier som ansluter med datalager i Azure-molnet och arbetar med **Azure Machine Learning**.  <br/> [Skärmbild](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** språk  | Exempel på i **Julia** som förklarar vi Plotting i Julia, deep learning i Julia, anropa C och Python från Julia osv. <br/> [Skärmbild](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Azure Machine Learning** AzureML  | Skapa ML och deep learning-modeller med **Azure Machine Learning** Service och distribuera modeller var som helst. Utnyttjar funktioner som automatisk ML Intelligent hyper parametern justering modellhantering, distribuerade utbildning. <br/> [Skärmbild](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** anteckningsböcker  | Deep Learning exempel som använder **PyTorch** baserat neurala nätverk. Det finns en mängd olika anteckningsböcker som sträcker sig från nybörjare till avancerade scenarier.  <br/> [Skärmbild](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Flera olika exempel Neurala nätverk och tekniker som implementeras med hjälp av den **TensorFlow** framework. <br/> [Skärmbild](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Exempel som publicerats av Cognitive Toolkit-teamet hos Microsoft för djupinlärning.  <br/> [Skärmbild](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | Deep Learning exempel som använder **caffe2** baserat neurala nätverk. Det finns flera anteckningsböcker som har utformats för att bekanta användare med caffe2 och hur du använder det på ett effektivt sätt, inklusive exempel som avbildning bearbeta data i förväg datauppsättningen skapas, Regression, och med före utbildade modeller. <br/> [Skärmbild](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Python-baserade exempel som använder **H2O** för ett stort antal verkligt scenario problem. <br/> [Skärmbild](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** språk  | Exemplet genom att använda funktioner och funktionerna i Spark's **MLlib** toolkit via **pySpark** och **MMLSpark - Microsoft Machine Learning för Apache Spark** på **Apache Spark-2.x**.  <br/> [Skärmbild](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Standard Machine Learning-exemplen i **XGBoost** för scenarier som klassificering, regression osv. <br/> [Skärmbild](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Åtkomst Jupyter 

Du kan komma åt Jupyter genom att klicka på den `Jupyter` -ikonen i menyn skrivbord eller ett program. Du kan också öppna Jupyter på Linux-versioner av DSVM via en fjärranslutning från en webbläsare genom att besöka **`https://<Full Domain Name or IP Address of the DSVM>:8000`** på Ubuntu.

Se skärmbilden att lägga till undantag och aktivera Jupyter åtkomst via webbläsaren.


![Aktivera Jupyter-undantag](./media/ubuntu-jupyter-exception.png)


Logga in med samma lösenord som din inloggning för DSVM.
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

