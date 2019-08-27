---
title: Djup inlärning & AI-ramverk
titleSuffix: Azure Data Science Virtual Machines
description: Ramverk för djup inlärning och verktyg på Azure Data Science Virtual Machine, inklusive TensorFlow, PyTorch, keras, caffe, MXNet, Horovod,, kedjer med mera.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: 3c9510b99bbdc7db292ecdc0bf59aadc305cf0c8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035846"
---
# <a name="deep-learning-and-ai-frameworks"></a>Ramverk för djupinlärning och AI
[Data science Virtual Machine](https://aka.ms/dsvm) (DSVM) stöder ett antal djup inlärnings ramverk som hjälper dig att bygga AI-program (artificiell intelligens) med förutsägelse analyser och kognitiva funktioner som bild-och språk förståelse.

Ramverk för djup inlärning som är tillgängliga i DSVM är:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet Model-Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Djupgående Water
+ NVIDIA SIFFROR
+ NVIDIA smi
+ TensorFlow betjänar
+ TensorRT
+ Microsoft Cognitive Toolkit

|DL&nbsp;-&nbsp;verktygpå&nbsp;DSVM|Windows|Linux|Användnings&nbsp;anmärkningar|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Ja (Windows 2016) | Ja |Installeras i python 3,5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Exempel på Jupyter-anteckningsböcker ingår på DSVM.<br/><br/>**Så här kör du det**:<br/>* Terminal: aktivera rätt miljö och kör sedan python. <br/> Jupyter Anslut till [Jupyter](provision-vm.md#tools) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna TensorFlow-katalogen för exempel.  |
|[PyTorch](https://pytorch.org/)| Nej | Ja |Installerat i [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Exempel på Jupyter-anteckningsböcker ingår och exempel finns i/dsvm/samples/pytorch.    <br/><br/>**Så här kör du det**<br/>* Terminal: aktivera rätt miljö och kör sedan python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Anslut och öppna PyTorch-katalogen för exempel.  |
|[Keras](https://keras.io/)| Ja | Ja |API installeras i python 3,5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Exempel](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Så här kör du det**:<br/>* Terminal: aktivera rätt miljö och kör sedan python. <br/> Jupyter Hämta exemplen från GitHub-platsen, Anslut till [Jupyter](provision-vm.md#tools) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna exempel katalogen. |
|[Caffe](https://github.com/caffe2/caffe2) | Nej |Ja (Ubuntu)|Caffe är installerat i `/opt/caffe`.   Exempel finns i `/opt/caffe/examples`. <br/><br/>**Kör det**genom att använda X2Go för att logga in på den virtuella datorn och sedan starta en ny terminal och ange:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Ett nytt webbläsarfönster öppnas med exempelanteckningsböcker. Binärfiler installeras i /opt/caffe/build/install/bin.<br/><br/>Installerade versionen av Caffe kräver Python 2.7 och fungerar inte med Python 3.5 aktiverat som standard. Om du vill växla till python 2,7 `source activate root` kör du för att växla Anaconda-miljö.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nej |Ja (Ubuntu)|Caffe2 installeras i den [conda-miljö för Python 2.7 (rot)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Källan är i `/opt/caffe2`.<br/>Exempelanteckningsböcker som ingår i JupyterHub.<br/><br/>**Så här kör du det**:<br/>* I terminalen: aktivera den [rot Python-miljön](dsvm-languages.md#python-linux-and-windows-server-2012-edition), starta Python och importera caffe2. <br/> * I JupyterHub: [ansluta till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), gå sedan till katalogen Caffe2 att hitta exempelanteckningsböcker. Vissa anteckningsböcker kräver Caffe2 roten anges i Python-kod. Ange /opt/caffe2. |
|[Torch](http://torch.ch/) | Nej |Ja (Ubuntu)|Torch är installerat i `/dsvm/tools/torch`. PyTorch är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö. Torch-exempel är `/dsvm/samples/torch` i och PyTorch-exempel `/dsvm/samples/pytorch`finns i. |
|[MXNet](https://mxnet.io/) | Ja (Windows 2016) | Ja|MXNet är installerat i `C:\dsvm\tools\mxnet` på Windows och `/dsvm/tools/mxnet` i Linux. Python-bindningar som är installerade i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3.6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-bindningar också har installerats på Ubuntu.<br/><br/>Exempel Jupyter-anteckningsböcker ingår. <br/><br/>**Så här kör du det**:<br/>Slutliga Aktivera rätt miljö och kör sedan python. <br/> Jupyter Anslut till [Jupyter](provision-vm.md#tools) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna mxnet-katalogen för exempel.|
|[MXNet-modell Server](https://github.com/awslabs/mxnet-model-server) | Nej | Ja |En server för att skapa HTTP-slutpunkter för MXNet-och ONNX-modeller. _mxnet-modell-server_ är tillgängliga på terminalen. Exempel på [sidan för MXNet-modell servern](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nej | Ja (Ubuntu) |Ramverk för distribuerad djup inlärning för TensorFlow. Horovod installeras i Python 3.5 på [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Se exempel](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**Om du vill köra den**aktiverar du rätt miljö vid terminalen och kör sedan python. |
|[Theano](https://github.com/Theano/Theano) | Nej | Ja (Ubuntu) |Theano är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö.<br/><br/>**Så här kör du det**: <br/>Slutliga Aktivera den python-version du vill använda (rot-eller py35), kör python och importera Theano.<br/>Jupyter Välj python 2,7-eller 3,5-kärnan och importera sedan Theano.  <br/>Undvik en senaste MKL bugg, måste du först ställa in MKL threading lager:<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainer](https://chainer.org/) |Nej | Ja |Chainer är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL och ChainerCV är installerade. <br/><br/>Exempelanteckningsböcker som ingår i JupyterHub.<br/><br/>**Så här kör du det**: <br/>Slutliga Aktivera [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -miljön, kör _python_och importera sedan en kedja. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och gå sedan till katalogen kedjar för att hitta exempel antecknings böcker.|
|[NVidia-siffror](https://github.com/NVIDIA/DIGITS) | Nej | Ja (Ubuntu) |Djup inlärnings system från NVIDIA för att snabbt träna djupgående inlärnings modeller. SIFFROR är installerat i `/dsvm/tools/DIGITS` och finns tillgänglig kallas för en tjänst _siffror_.  <br/><br/>**Så här kör du det**: <br/>Logga in på den virtuella datorn med X2Go. Starta tjänsten ```sudo systemctl start digits```vid en Terminal. <br/><br/>Tjänsten tar ungefär en minut för att starta. Starta en webbläsare och navigera till `http://localhost:5000`. Observera att SIFFROR ger inte en säker inloggning och bör inte exponeras utanför den virtuella datorn.|
|[CUDA, cuDNN, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit) |Ja | Ja | |
|NVIDIA smi|Ja | Ja |NVIDIA-verktyg för att fråga GPU-aktivitet. _NVIDIA smi_ finns på systemsökvägen. <br/><br/>Starta en kommandotolk (på Windows) eller en terminal (på Linux) och kör sedan _nvidia smi_.|
|[TensorFlow-servar](https://www.tensorflow.org/serving/) | Nej | Ja |En server som ska utformas för en TensorFlow modell. _tensorflow_model_server_ finns på terminalen. Exempel finns [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nej | Ja (Ubuntu) |En djupinlärning inferens servern från NVIDIA. TensorRT installeras som en _apt_ paketet. Exempel finns [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Ja | Ja | Installeras i python 3,5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Exempel på Jupyter-anteckningsböcker ingår på DSVM. <br/><br/>**Så här kör du det**: <br/>Slutliga Aktivera rätt miljö och kör python. <br/>Jupyter Anslut till [Jupyter](provision-vm.md#tools) eller [JUPYTERHUB](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna CNTK-katalogen för exempel. |
|Djupgående Water|Nej | Ja (Ubuntu) |Ramverk för djup inlärning för H2O, djup vattnet installeras i [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och är också tillgängligt i `/dsvm/tools/deep_water`. Exempelanteckningsböcker som ingår i JupyterHub. Djupgående Water kräver CUDA 8 med cuDNN 5.1. Detta är inte på en bibliotekssökväg som standard eftersom andra ramverk för djupinlärning använder CUDA-9 och cuDNN 7. Använda CUDA 8 + cuDNN 5.1 för djupgående Water:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Använda djup Water:<br/>* Terminal: aktivera [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -miljön och kör sedan _python_. <br/>* JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och navigera sedan till deep_water-katalogen för att hitta exempel antecknings böcker.|
