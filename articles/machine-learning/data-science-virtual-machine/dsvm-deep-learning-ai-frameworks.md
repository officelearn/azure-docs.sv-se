---
title: Ramverk för Djupinlärning och AI - Azure | Microsoft Docs
description: Läs mer om djupinlärning ramverk och verktyg som stöds på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: b5bf9a0e9cbbcf2a4144255ac0bfbd4bd48e956c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104477"
---
# <a name="deep-learning-and-ai-frameworks"></a>Ramverk för djupinlärning och AI
Den [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) och [Deep Learning VM](https://aka.ms/dsvm/deeplearning) stöder ett antal olika ramverk för djupinlärning för att skapa artificiell intelligens (AI) program med förutsägelseanalys och kognitiva funktioner som bild- och språkförståelse. 

Här följer information på alla deep learning-ramverk som är tillgängliga på DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Microsoft Cognitive Toolkit (CNTK) är installerad i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3.6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.     |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / köra den?    | * I en terminal: aktivera rätt miljö och sedan köra Python. <br/>
 * I Jupyter: Ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen CNTK för exempel. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | TensorFlow installeras i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3.6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.     |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / köra den?    | * I en terminal: aktivera rätt miljö och sedan köra Python. <br/>
 * I Jupyter: Ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna sedan TensorFlow-katalogen för exempel.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Vad är det?   | Distribued djupinlärning ramverk för TensorFlow      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Horovod installeras i Python 3.5 på [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Innehåller länkar till exempel      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Relaterade verktyg på DSVM      | TensorFlow      |
| Hur du använder / köra den?    | I en terminal: aktivera rätt miljö och sedan köra Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Vad är det?   | Övergripande djupinlärning API      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | TensorFlow installeras i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3.6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Innehåller länkar till exempel      | https://github.com/fchollet/keras/tree/master/examples      |
| Relaterade verktyg på DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Hur du använder / köra den?    | * I en terminal: aktivera rätt miljö och sedan köra Python. <br/>
 * I Jupyter: Ladda ned exempel från Github-plats, ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen exemplet. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe är installerat i `/opt/caffe`.    |
| Växla till Python 2.7 | Kör `source activate root` |
| Innehåller länkar till exempel      | Exempel som ingår i `/opt/caffe/examples`.      |
| Relaterade verktyg på DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Hur du använder / köra den?  

Använd X2Go för att logga in till den virtuella datorn och sedan starta en ny terminal och ange

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Ett nytt webbläsarfönster öppnas med exempelanteckningsböcker.

Binärfiler installeras i /opt/caffe/build/install/bin.

Installerade versionen av Caffe kräver Python 2.7 och fungerar inte med Python 3.5 aktiverat som standard. Kör `source activate root` växla Anaconda miljö. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe2 installeras i den [conda-miljö för Python 2.7 (rot)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Källan är i `/opt/caffe2`. |
| Innehåller länkar till exempel      | Exempelanteckningsböcker som ingår i JupyterHub. |
| Relaterade verktyg på DSVM      | Caffe      |
| Hur du använder / köra den?    | * I terminalen: aktivera den [rot Python-miljön](dsvm-languages.md#python-linux-and-windows-server-2012-edition), starta Python och importera caffe2. <br/> * I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå sedan till katalogen Caffe2 att hitta exempelanteckningsböcker. Vissa anteckningsböcker kräver Caffe2 roten anges i Python-kod. Ange /opt/caffe2. |
| Skapa anteckningar | Caffe2 bygger på Linux från källan och innehåller CUDA-, cuDNN- och Intel MKL. Den aktuella allokering är 0d9c0d48c6f20143d6404b99cc568efd29d5a4be som har valts för stabilitet på alla GPU: er och prover. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | Chainer är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL och ChainerCV är installerade.   |
| Innehåller länkar till exempel      | Exempelanteckningsböcker som ingår i JupyterHub. |
| Relaterade verktyg på DSVM      | Caffe      |
| Hur du använder / köra den?  | * I terminalen: aktivera den [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) miljö, kör _python_, importera chainer. <br/>
* I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå sedan till katalogen Chainer att hitta exempelanteckningsböcker.


## <a name="deep-water"></a>Djupgående Water

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning för H2O      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Djupgående Water är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och är också tillgängligt i `/dsvm/tools/deep_water`.   |
| Innehåller länkar till exempel      | Exempelanteckningsböcker som ingår i JupyterHub.      |
| Relaterade verktyg på DSVM      | H2O Sparkling Water      |

### <a name="how-to-use--run-it"></a>Hur du använder / köra den?  

Djupgående Water kräver CUDA 8 med cuDNN 5.1. Detta är inte på en bibliotekssökväg som standard eftersom andra ramverk för djupinlärning använder CUDA-9 och cuDNN 7. Använda CUDA 8 + cuDNN 5.1 för djupgående Water:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Använda djup Water:
* I terminalen: aktivera den [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) miljö, kör sedan _python_. <br/>
* I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå sedan till katalogen deep_water att hitta exempelanteckningsböcker.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | MXNet är installerat i `C:\dsvm\tools\mxnet` på Windows och `/dsvm/tools/mxnet` i Linux. Python-bindningar som är installerade i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3.6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-bindningar också har installerats på Ubuntu.   |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår.    |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / köra den?    | * I en terminal: aktivera rätt miljö och sedan köra Python. <br/>
 * I Jupyter: Ansluta till [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen mxnet för exempel.  |
 | Skapa anteckningar | MXNet bygger på Linux från källan. Den här versionen innehåller CUDA, cuDNN-, NCCL och MKL. |

## <a name="nvidia-digits"></a>NVIDIA SIFFROR

|    |           |
| ------------- | ------------- |
| Vad är det?   | Djupinlärning system från NVIDIA för att snabbt träna modeller för djup maskininlärning      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | SIFFROR är installerat i `/dsvm/tools/DIGITS` och finns tillgänglig kallas för en tjänst _siffror_.   |
### <a name="how-to-use--run-it"></a>Hur du använder / köra den?  

Logga in på den virtuella datorn med X2Go. Starta tjänsten på en terminal:

    sudo systemctl start digits

Tjänsten tar ungefär en minut för att starta. Starta en webbläsare och navigera till `http://localhost:5000`. Observera att SIFFROR ger inte en säker inloggning och bör inte exponeras utanför den virtuella datorn.



## <a name="nvidia-smi"></a>NVIDIA smi

|    |           |
| ------------- | ------------- |
| Vad är det?   | NVIDIA-verktyget för att fråga GPU-aktivitet      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | _NVIDIA smi_ finns på systemsökvägen.   |
| Hur du använder / köra den? | Starta en kommandotolk (på Windows) eller en terminal (på Linux) och kör sedan _nvidia smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Theano är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Relaterade verktyg på DSVM      | Keras      |
| Hur du använder / köra den?    | * Aktivera Python-version du vill (rot- eller py35), köra python och sedan importera theano i en terminal. <br/> 
* Välj Python 2.7 eller 3,5 kernel i Jupyter, och sedan importera theano.  
<br/>
Undvik en senaste MKL bugg, måste du först ställa in MKL threading lager:<br/><br/>
_Exportera MKL_THREADING_LAYER = GNU_
|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | Torch är installerat i `/dsvm/tools/torch`. PyTorch är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.   |
| Innehåller länkar till exempel      | Torch exemplen finns på `/dsvm/samples/torch`. PyTorch-exempel finns på `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Vad är det?   | Ramverk för djupinlärning      |
| Stöds DSVM-versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | PyTorch är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker ingår och exempel finns också i /dsvm/samples/pytorch.      |
| Relaterade verktyg på DSVM      | Torch      |
| Hur du använder / det. | 
* I en terminal: aktivera rätt miljö och sedan köra Python. <br/>
 * I Jupyter: Ansluta till [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), öppna katalogen PyTorch för exempel.  |

## <a name="mxnet-model-server"></a>MXNet Model-Server

|    |           |
| ------------- | ------------- |
| Vad är det?   | En server för att skapa HTTP-slutpunkter för MXNet och ONNX-modeller      |
| Stöds DSVM-versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | _mxnet-modell-server_ är tillgängliga på terminalen.   |
| Innehåller länkar till exempel      | Leta efter uppdaterade exempel på den [MXNet modellen serversidan](https://github.com/awslabs/mxnet-model-server).    |
| Relaterade verktyg på DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow betjänar

|    |           |
| ------------- | ------------- |
| Vad är det?   | En server för att köra inferensjobb på en TensorFlow-modell      |
| Stöds DSVM-versioner      | Linux     |
| Hur är det konfigurerade / installerad på DSVM?  | _tensorflow_model_server_ finns på terminalen.   |
| Innehåller länkar till exempel      | Exempel finns [online](https://www.tensorflow.org/serving/).      |
| Relaterade verktyg på DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Vad är det?   | En djupinlärning inferens servern från NVIDIA. |
| Stöds DSVM-versioner      | Ubuntu     |
| Hur är det konfigurerade / installerad på DSVM?  | TensorRT installeras som en _apt_ paketet.   |
| Innehåller länkar till exempel      | Exempel finns [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Relaterade verktyg på DSVM      | TensorFlow som betjänar MXNet Model-Server  |



