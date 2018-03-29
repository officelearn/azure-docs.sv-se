---
title: Exemplen och genomgång för den datavetenskap virtuella - Azure | Microsoft Docs
description: Exemplen och genomgång för datavetenskap virtuell dator.
keywords: datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 6c5b5c8c9dbb03a2189eb5f1539499ace55e7dfc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Exempel på datavetenskap virtuella datorer (DSVM)

DSVMs medföljer inkluderade fullständigt arbetat ut exempel i form av Jupyter-anteckningsböcker och andra som inte är baserade på Jupyter. Du kan komma åt Jupyter genom att klicka på den `Jupyter` ikon på menyn skrivbord eller ett program.  
> [!NOTE]
> Referera till [åtkomst Jupyter](#access-jupyter) avsnittet för att aktivera Jupyter-anteckningsböcker på din DSVM.

## <a name="quick-reference-of-samples"></a>Snabbreferens av prover
| Exempel kategori | Beskrivning | Platser |
| ------------- | ------------- | ------------- |
| **R** språk  | Exempel på **R** förklarar scenarier som ansluter med Azure-molndata lagras jämföra öppen källa R och Microsoft R & operationalizing modeller på Microsoft R Server eller SQL Server. <br/> [Skärmbild](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** språk  | Exempel på **Python** förklarar scenarier som ansluter till Azure-molnet datalager och arbeta med **Azure Machine Learning**.  <br/> [Skärmbild](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** språk  | Exempel på **Julia** som innehåller information om Plotting i Julia djupt Lär i Julia, anropa C och Python från Julia osv. <br/> [Skärmbild](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft kognitiva Toolkit)  | Djup learning prover som publicerats av kognitiva Toolkit-team på Microsoft.  <br/> [Skärmbild](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXNet** bärbara datorer  | Djupt Learning exempel som använder **MXNet** baserat neurala nätverk. Det finns en mängd bärbara datorer, från nybörjare till avancerade scenarier.  <br/> [Skärmbild](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | Interaktion med **Azure Machine Learning** Studio och skapa webbtjänsten slutpunkter från lokalt tränade modeller för molnbaserade bedömningsprofil arbetsflöden. <br/> [Skärmbild](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Djupt Learning exempel som använder **caffe2** baserat neurala nätverk. Det finns flera anteckningsböcker som utformats för att bekanta användare med caffe2 och hur du använder den på ett effektivt sätt, inklusive exempel som bild förbearbetning dataset skapas, Regression, och använder tränas före modeller. <br/> [Skärmbild](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Python-baserade exempel som använder **H2O** för många verkligt scenario problem. <br/> [Skärmbild](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** språk  | Exempel som använder funktioner och förmågor i Sparks **MLlib** toolkit via **pySpark 2.0** på **Apache Spark 2.0**.  <br/> [Skärmbild](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** språk  | Exempel som använder olika **MMLSpark - Microsoft Machine Learning för Apache Spark**, vilket är ett ramverk som innehåller ett antal djup inlärning och datavetenskap verktyg för **Apache Spark**. <br/> [Skärmbild](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Flera olika Neurala nätverk exempel och tekniker som implementeras med hjälp av den **TensorFlow** framework. <br/> [Skärmbild](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Standard Machine Learning-exempel i **XGBoost** för scenarier som klassificering, regression osv. <br/> [Skärmbild](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Åtkomst Jupyter 

Besök Jupyter hem genom att gå till **`https://localhost:9999`** i Windows eller **`https://localhost:8000`** på Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Aktivera Jupyter åtkomst från webbläsare

**Windows DSVM**

Kör **`Jupyter SetPassword`** från genväg på skrivbordet och följ anvisningarna för att ange/återställning av lösenordet för Jupyter och starta Jupyter bearbeta. 
<br/>![Aktivera Jupyter-undantag](./media/jupyter-setpassword.png)<br/>
Du kan komma åt Jupyter hem när Jupyter-processen har startats på den virtuella datorn genom att besöka **`https://localhost:9999`** i webbläsaren. Se skärmbilden att lägga till undantag och aktivera Jupyter åtkomst via webbläsaren
<br/>![Aktivera Jupyter-undantag](./media/windows-jupyter-exception.png)<br/>
Logga in med det nya lösenordet som du bara har angett.
<br/>
**Linux DSVM**

Du kan komma åt Jupyter hem på den virtuella datorn genom att besöka **`https://localhost:8000`** i webbläsaren. Se skärmbilden att lägga till undantag och aktivera Jupyter åtkomst via webbläsaren.
<br/>![Aktivera Jupyter-undantag](./media/ubuntu-jupyter-exception.png)<br/>
Logga in med samma lösenord som din inloggningsinformation för DSVM.
<br/>

**Startsida för Jupyter**
<br/>![Startsida för Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R-språk 
<br/>![R-exempel](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python-språk
<br/>![Python-exempel](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia språk 
<br/>![Julia prover](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![CNTK-exempel](./media/cntk-samples2.png)<br/>
<br/>![CNTK-exempel](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![MXnet-exempel](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML Samples](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![caffe2-exempel](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O-exempel](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML-exempel](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-exempel](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost-exempel](./media/xgboost-samples.png)<br/>

