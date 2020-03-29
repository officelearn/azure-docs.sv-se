---
title: Exempel program & ML genomgångar
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du hanterar vanliga uppgifter och scenarier med den virtuella datorn för datavetenskap genom dessa exempel och genomgångar.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900052"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Exempel på virtuella Azure Data Science-datorer

Virtuella Azure Data Science-datorer (DSVM) innehåller en omfattande uppsättning exempelkod. Dessa exempel inkluderar Jupyter anteckningsböcker och skript på språk som Python och R.
> [!NOTE]
> Mer information om hur du kör Jupyter-anteckningsböcker på virtuella datascience-datorer finns i avsnittet [Access Jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Krav

För att kunna köra dessa exempel måste du ha etablerat en virtuell dator för datavetenskap. Se snabbstarterna för [Windows](./provision-vm.md) och [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Tillgängliga exempel
| Kategorin Exempel | Beskrivning | Platser |
| ------------- | ------------- | ------------- |
| R-språk  | Exempel illustrerar scenarier som hur du ansluter till Azure-baserade molndatalager och hur du jämför R och Microsoft Machine Learning Server med öppen källkod. De förklarar också hur du kan operationalisera modeller på Microsoft Machine Learning Server och SQL Server. <br/> [R-språk](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python-språk  | Exempel förklarar scenarier som hur du ansluter till Azure-baserade molndatalager och hur du arbetar med Azure Machine Learning.  <br/> [Python-språk](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia språk  | Ger en detaljerad beskrivning av plottning och djupinlärning i Julia. Förklarar också hur man ringer C och Python från Julia. <br/> [Julia språk](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Illustrerar hur du bygger maskininlärnings- och djupinlärningsmodeller med Maskininlärning. Distribuera modeller var som helst. Använd automatiserad maskininlärning och intelligent hyperparameterjustering. Använd även modellhantering och distribuerad utbildning. <br/> [Maskininlärning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch-anteckningsböcker  | Djupinlärningsprover som använder PyTorch-baserade neurala nätverk. Anteckningsböcker sträcker sig från nybörjare till avancerade scenarier.  <br/> [PyTorch-anteckningsböcker](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  En mängd olika neurala nätverksprover och tekniker som implementeras med hjälp av TensorFlow-ramverket. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Djupinlärningsexempel som publicerats av Cognitive Toolkit-teamet på Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Djupinlärningsprover som använder Caffe2-baserade neurala nätverk. Flera bärbara datorer bekanta användare med Caffe2 och hur man använder den effektivt. Exempel är bildförbearbetning och skapande av datauppsättning. De inkluderar också regression och hur man använder förtränade modeller. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2o   | Python-baserade exempel som använder H2O för verkliga problemscenarier. <br/> [H2o](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML-språk  | Exempel som använder funktionerna i Apache Spark MLLib-verktygslådan genom pySpark och MMLSpark: Microsoft Machine Learning for Apache Spark på Apache Spark 2.x.  <br/> [SparkML-språk](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standard machine-learning-exempel i XGBoost för scenarier som klassificering och regression. <br/> [XGBoost (mycket)](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Tillgång Jupyter 

Om du vill komma åt Jupyter väljer du **Jupyter-ikonen** på skrivbordet eller programmenyn. Du kan också komma åt Jupyter på en Linux-utgåva av en DSVM. Om du vill fjärransluta från `https://<Full Domain Name or IP Address of the DSVM>:8000` en webbläsare går du till Ubuntu.

Om du vill lägga till undantag och göra Jupyter-åtkomst tillgänglig via en webbläsare använder du följande vägledning:


![Aktivera Jupyter-undantag](./media/ubuntu-jupyter-exception.png)


Logga in med samma lösenord som du använder för att logga in på den virtuella datorn för datavetenskap.
<br/>

**Jupyter hem**
<br/>![Jupyter hem](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R-språk 
<br/>![R-prover](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python-språk
<br/>![Python-exempel](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia språk 
<br/>![Julia-exempel](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Exempel på Azure Machine Learning](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch prover](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-exempel](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-prover](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 prover](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2o 
<br/>![H2O-prover](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML (gnista) 
<br/>![SparkML-exempel](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost prover](./media/xgboost-samples.png)<br/>

