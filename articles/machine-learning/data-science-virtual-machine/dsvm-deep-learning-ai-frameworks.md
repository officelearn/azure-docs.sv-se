---
title: "Djupgående inlärning och AI ramverk - Azure | Microsoft Docs"
description: "Ramverk för djupinlärning och AI"
keywords: "datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: c6dba433536e07970ad5ce338e33b5d19f4532b2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Ramverk för djupinlärning och AI
Den [datavetenskap virtuella](http://aka.ms/dsvm) (DSVM) och [djup Learning VM](http://aka.ms/dsvm/deeplearning) stöder ett antal djup learning ramverk för att bygga program styrs av datorn (AI) med förutsägelseanalyser och kognitiva funktioner som bilden och förstå språk. 

Här följer information på alla djup learning ramverk som är tillgängliga på DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Microsoft kognitiva Toolkit (CNTK) är installerat i Python 2.7, i den _rot_ miljö, samt Python 3.5 i den _py35_ miljö.   |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.     |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | Öppna Jupyter och sedan letar du upp mappen CNTK  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Linux, TensorFlow är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö. Windows, Tensorflow är installerat i Python 3.5 (_py35_) miljö.  |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.     |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | Öppna Jupyter och sedan letar du upp mappen TensorFlow.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Keras är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Länkar till exempel      | https://github.com/fchollet/keras/tree/master/examples      |
| Relaterade verktyg på DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Hur du använder / kör den?    | Hämta exempel från Github-plats, kopierar den till en katalog under ~/notebooks och öppna den i Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe installeras i `/opt/caffe`.    |
| Länkar till exempel      | Exempel som ingår i `/opt/caffe/examples`.      |
| Relaterade verktyg på DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Använd X2Go för att logga in på den virtuella datorn och sedan starta en ny terminal och ange

```
cd /opt/caffe/examples
jupyter notebook
```

Ett nytt webbläsarfönster öppnas med exempel bärbara datorer.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe2 installeras i `/opt/caffe2`. Det är också tillgängligt för Python 2.7 (_rot_) conda miljö.     |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår     |
| Relaterade verktyg på DSVM      | Caffe      |
| Hur du använder / kör den?    | Öppna Jupyter och sedan bläddra till katalogen Caffe2 att hitta exempel bärbara datorer. Vissa datorer kräver Caffe2 roten anges i Python-kod. Ange /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Klientprocess installeras i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö. ChainerRL och ChainerCV är installerade.   |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.      |
| Relaterade verktyg på DSVM      | Caffe      |

### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Aktivera Python-version som du vill använda i en terminal (_rot_ eller _py35_) kör _python_, importera klientprocess. Välj Python 2.7 eller 3.5 kernel i Jupyter, och sedan importera klientprocess.


## <a name="deep-water"></a>Djupgående vattenstämplar

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning ramverk för H2O      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Djupgående vattenstämplar är installerat i `/dsvm/tools/deep_water`.   |
| Länkar till exempel      | Exempel är tillgängliga via djup vattenstämplar-servern.      |
| Relaterade verktyg på DSVM      | H2O mousserande vattenstämplar      |

### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Ansluta till den virtuella datorn med hjälp av X2Go. Starta servern djup vattenstämplar i en terminal:

    java -jar /dsvm/tools/deep_water/h2o.jar

Öppna en webbläsare och Anslut till `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | MXNet är installerat i `C:\dsvm\tools\mxnet` i Windows och `/dsvm/tools/mxnet` på Linux. Python-bindningar är installerade i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö. R-bindningar är installerade.   |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.    |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | Öppna Jupyter och sedan letar du upp mappen mxnet  |

## <a name="nvidia-digits"></a>NVIDIA SIFFROR

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djup learning system från NVIDIA för att snabbt träna djup learning-modeller      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | SIFFROR är installerat i `/dsvm/tools/DIGITS` och är tillgänglig kallas för en tjänst _siffror_.   |
### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Logga in på den virtuella datorn med X2Go. Starta tjänsten på en terminal:

    sudo systemctl start digits

Tjänsten tar ungefär en minut för att starta. Starta en webbläsare och gå till `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Vad är det?   | NVIDIA-verktyget för att fråga efter GPU-aktivitet      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | _NVIDIA smi_ finns på systemsökvägen.   |
| Hur du använder / kör den? | Starta en kommandotolk (på Windows) eller en terminal (på Linux) och kör sedan _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Theano är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | Aktivera Python-version du vill (rot eller py35), kör python och sedan importera theano i en terminal. Välj Python 2.7 eller 3.5 kernel i Jupyter, och sedan importera theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Torch installeras i `/dsvm/tools/torch`. PyTorch är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Länkar till exempel      | Torch exempel finns i `/dsvm/samples/torch`. PyTorch exempel finns i `/dsvm/samples/pytorch`.      |

