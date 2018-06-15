---
title: Djupgående inlärning och AI ramverk - Azure | Microsoft Docs
description: Ramverk för djupinlärning och AI
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: cd8579fe29282f3875ecfddf67a34444e3de9ef5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160758"
---
# <a name="deep-learning-and-ai-frameworks"></a>Ramverk för djupinlärning och AI
Den [datavetenskap virtuella](http://aka.ms/dsvm) (DSVM) och [djup Learning VM](http://aka.ms/dsvm/deeplearning) stöder ett antal djup learning ramverk för att bygga program styrs av datorn (AI) med förutsägelseanalyser och kognitiva funktioner som bilden och förstå språk. 

Här följer information på alla djup learning ramverk som är tillgängliga på DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Microsoft kognitiva Toolkit (CNTK) är installerad i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.     |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | * På en terminal: aktivera rätt miljön, och kör sedan Python. <br/>
 * I Jupyter: Ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen CNTK efter exempel. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | TensorFlow installeras i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.     |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | * På en terminal: aktivera rätt miljön, och kör sedan Python. <br/>
 * I Jupyter: Ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen TensorFlow efter exempel.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Vad är det?   | Distribued djup learning ramverk för TensorFlow      |
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Horovod installeras i Python 3.5 på [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Länkar till exempel      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Relaterade verktyg på DSVM      | TensorFlow      |
| Hur du använder / kör den?    | I en terminal: aktivera rätt miljön, och kör sedan Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | TensorFlow installeras i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Länkar till exempel      | https://github.com/fchollet/keras/tree/master/examples      |
| Relaterade verktyg på DSVM      | Microsoft kognitiva Toolkit, TensorFlow, Theano      |
| Hur du använder / kör den?    | * På en terminal: aktivera rätt miljön, och kör sedan Python. <br/>
 * I Jupyter: Ladda ned exempel från Github-plats, Anslut till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen exempel. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe installeras i `/opt/caffe`.    |
| Växla till Python 2.7 | Kör `source activate root` |
| Länkar till exempel      | Exempel som ingår i `/opt/caffe/examples`.      |
| Relaterade verktyg på DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Använd X2Go för att logga in på den virtuella datorn och sedan starta en ny terminal och ange

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Ett nytt webbläsarfönster öppnas med exempel bärbara datorer.

Binärfiler installeras i /opt/caffe/build/install/bin.

Installerade versionen av Caffe kräver Python 2.7 och fungerar inte med Python 3.5 aktiverat som standard. Kör `source activate root` växla Anaconda miljö. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe2 installeras i den [miljö för Python 2.7 (rot) conda](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Källan är i `/opt/caffe2`. |
| Länkar till exempel      | Exempel bärbara datorer ingår i JupyterHub. |
| Relaterade verktyg på DSVM      | Caffe      |
| Hur du använder / kör den?    | * På terminalen: aktivera den [rot Python miljö](dsvm-languages.md#python-linux-and-windows-server-2012-edition), starta Python och importera caffe2. <br/> * I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå till katalogen Caffe2 att hitta exempel bärbara datorer. Vissa datorer kräver Caffe2 roten anges i Python-kod. Ange /opt/caffe2. |
| Skapa anteckningar | Caffe2 bygger på Linux från källan och omfattar CUDA, cuDNN och Intel MKL. Aktuellt utförda är 0d9c0d48c6f20143d6404b99cc568efd29d5a4be som valdes för stabiliteten i alla GPU-kort och prover. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Klientprocess installeras i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL och ChainerCV är installerade.   |
| Länkar till exempel      | Exempel bärbara datorer ingår i JupyterHub. |
| Relaterade verktyg på DSVM      | Caffe      |
| Hur du använder / kör den?  | * På terminalen: aktivera den [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) miljö, köra _python_, importera klientprocess. <br/>
* I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå till katalogen klientprocess att hitta exempel bärbara datorer.


## <a name="deep-water"></a>Djupgående vattenstämplar

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning ramverk för H2O      |
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Djupgående vattenstämplar är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och finns också i `/dsvm/tools/deep_water`.   |
| Länkar till exempel      | Exempel bärbara datorer ingår i JupyterHub.      |
| Relaterade verktyg på DSVM      | H2O mousserande vattenstämplar      |

### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Djupgående vattenstämplar kräver CUDA 8 cuDNN 5.1. Detta är inte på en bibliotekssökväg som standard som andra djup learning ramverk Använd CUDA 9 och cuDNN 7. Använda CUDA 8 + cuDNN 5.1 för djup vattenstämplar:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Använda djup vattenstämplar:
* I terminalen: aktivera den [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) miljö, kör _python_. <br/>
* I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå till katalogen deep_water att hitta exempel bärbara datorer.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | MXNet är installerat i `C:\dsvm\tools\mxnet` i Windows och `/dsvm/tools/mxnet` på Linux. Python-bindningar är installerade i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-bindningar installeras även på Ubuntu.   |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.    |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | * På en terminal: aktivera rätt miljön, och kör sedan Python. <br/>
 * I Jupyter: Ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen mxnet efter exempel.  |
 | Skapa anteckningar | MXNet bygger på Linux från källan. Den här versionen innehåller CUDA, cuDNN, NCCL och MKL. |

## <a name="nvidia-digits"></a>NVIDIA SIFFROR

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djup learning system från NVIDIA för att snabbt träna djup learning-modeller      |
| Stöds DSVM versioner      | Ubuntu     |
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
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Theano är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / kör den?    | * Aktivera Python-version du vill (rot eller py35), kör python och sedan importera theano i en terminal. <br/> 
* Välj Python 2.7 eller 3.5 kernel i Jupyter, och sedan importera theano.  
<br/>
Undvik ett senaste MKL programfel, måste du först ställa in MKL trådmodell lager:<br/><br/>
_Exportera MKL_THREADING_LAYER = GNU_
|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Torch installeras i `/dsvm/tools/torch`. PyTorch är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Länkar till exempel      | Torch exempel finns i `/dsvm/samples/torch`. PyTorch exempel finns i `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupgående learning framework      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | PyTorch är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår och exempel finns i /dsvm/samples/pytorch.      |
| Relaterade verktyg på DSVM      | Torch      |
| Hur du använder / kör den | 
* I en terminal: aktivera rätt miljön, och kör sedan Python. <br/>
 * I Jupyter: Ansluta till [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen PyTorch efter exempel.  |

## <a name="mxnet-model-server"></a>MXNet Model-Server

|    |           |
| ------------- | ------------- |
| Vad är det?   | En server för att skapa HTTP-slutpunkter för MXNet och ONNX modeller      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | _mxnet-modell-server_ är tillgänglig på på terminalen.   |
| Länkar till exempel      | Sök efter uppdaterade exempel den [MXNet modellserver-sidan](https://github.com/awslabs/mxnet-model-server).    |
| Relaterade verktyg på DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow fungerar

|    |           |
| ------------- | ------------- |
| Vad är det?   | En server för att köra inferencing på en TensorFlow modell      |
| Stöds DSVM versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | _tensorflow_model_server_ är tillgänglig på terminalen.   |
| Länkar till exempel      | Exempel är tillgängliga [online](https://www.tensorflow.org/serving/).      |
| Relaterade verktyg på DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Vad är det?   | En djup learning härledning av servern från NVIDIA. |
| Stöds DSVM versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | TensorRT installeras som en _lgh_ paketet.   |
| Länkar till exempel      | Exempel är tillgängliga [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Relaterade verktyg på DSVM      | TensorFlow betjänar MXNet modellen Server  |



