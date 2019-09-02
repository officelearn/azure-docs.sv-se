---
title: Djup inlärning & AI-ramverk
titleSuffix: Azure Data Science Virtual Machine
description: Tillgängliga ramverk för djup inlärning och verktyg på Azure Data Science Virtual Machine, inklusive TensorFlow, PyTorch, keras, caffe, MXNet, Horovod,, kedjer med mera.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: f71a2cc5c0a430037b429aac4f3ec4dc5f5f933d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208118"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Djup inlärning och AI-ramverk för Azure Data Science VM
[Data science Virtual Machine](https://aka.ms/dsvm) (DSVM) stöder ett antal djup inlärnings ramverk som hjälper dig att bygga AI-program (artificiell intelligens) med förutsägelse analyser och kognitiva funktioner som bild-och språk förståelse.

Ramverk för djup inlärning som är tillgängliga via DSVM omfattar:

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

|Djup inlärnings&nbsp;verktyg&nbsp;på&nbsp;DSVM|Windows|Linux|Användnings&nbsp;anmärkningar|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | Ja (Windows 2016) | Ja |Installeras i python 3,5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Exempel på Jupyter-anteckningsböcker ingår på DSVM.<br/><br/>**Så här kör du det**:<br/>Slutliga Aktivera rätt miljö och kör sedan python. <br/> Jupyter Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna TensorFlow-katalogen för exempel.  |
|[PyTorch](https://pytorch.org/)| Nej | Ja |Installerat i [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Exempel på Jupyter-anteckningsböcker ingår och exempel finns i/dsvm/samples/pytorch.    <br/><br/>**Så här kör du det**:<br/>Slutliga Aktivera rätt miljö och kör sedan python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Anslut och öppna PyTorch-katalogen för exempel.  |
|[Keras](https://keras.io/)| Ja | Ja |API installeras i python 3,5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och i python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Se exempel](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Så här kör du det**:<br/>Slutliga Aktivera rätt miljö och kör sedan python. <br/> Jupyter Hämta exemplen från GitHub-platsen, Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna exempel katalogen. |
|[Caffe](https://github.com/caffe2/caffe2) | Nej |Ja (Ubuntu)|Caffe är installerat i `/opt/caffe`.   Exempel finns i `/opt/caffe/examples`. <br/><br/>**Kör det**genom att använda X2Go för att logga in på den virtuella datorn och sedan starta en ny terminal och ange följande:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Ett nytt webbläsarfönster öppnas med exempelanteckningsböcker. Binärfiler installeras i /opt/caffe/build/install/bin.<br/><br/>Den installerade versionen av Caffe kräver python 2,7 och fungerar inte med python 3,5, som aktive ras som standard. Om du vill växla till python 2,7 `source activate root` kör du för att växla till Anaconda-miljön.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nej |Ja (Ubuntu)|Caffe2 installeras i den [conda-miljö för Python 2.7 (rot)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Källan är i `/opt/caffe2`.<br/>Exempelanteckningsböcker som ingår i JupyterHub.<br/><br/>**Så här kör du det**:<br/>Slutliga Aktivera den här [roten i python-miljön](dsvm-languages.md#python-linux-and-windows-server-2012-edition), starta python och importera Caffe2. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och gå sedan till katalogen Caffe2 för att hitta exempel antecknings böcker. Vissa anteckningsböcker kräver Caffe2 roten anges i Python-kod. Ange /opt/caffe2. |
|[Torch](http://torch.ch/) | Nej |Ja (Ubuntu)|Torch är installerat i `/dsvm/tools/torch`. PyTorch är installerat i Python 2.7 (_rot_), samt Python 3.5 (_py35_) miljö. Torch-exempel är `/dsvm/samples/torch` i och PyTorch-exempel `/dsvm/samples/pytorch`finns i. |
|[MXNet](https://mxnet.io/) | Ja (Windows 2016) | Ja|MXNet är installerat i `C:\dsvm\tools\mxnet` på Windows och `/dsvm/tools/mxnet` i Linux. Python-bindningar som är installerade i Python 3.5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och Python 3.6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-bindningar också har installerats på Ubuntu.<br/><br/>Exempel Jupyter-anteckningsböcker ingår. <br/><br/>**Så här kör du det**:<br/>Slutliga Aktivera rätt miljö och kör sedan python. <br/> Jupyter Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna mxnet-katalogen för exempel.|
|[MXNet-modell Server](https://github.com/awslabs/mxnet-model-server) | Nej | Ja |En server för att skapa HTTP-slutpunkter för MXNet-och ONNX-modeller. _Mxnet-Model-Server_ är tillgänglig i terminalen. Exempel på [sidan för MXNet-modell servern](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nej | Ja (Ubuntu) |Ramverk för distribuerad djup inlärning för TensorFlow. Horovod installeras i Python 3.5 på [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Se exempel](https://github.com/uber/horovod/tree/master/examples).<br/><br/>**Om du vill köra den**aktiverar du rätt miljö vid terminalen och kör sedan python. |
|[Theano](https://github.com/Theano/Theano) | Nej | Ja (Ubuntu) |Theano installeras i python 2,7 (_root_) och i python 3,5 (_py35_)-miljö.<br/><br/>**Så här kör du det**: <br/>Slutliga Aktivera den python-version som du vill använda (root eller py35), kör python och importera sedan Theano.<br/>Jupyter Välj python 2,7-eller 3,5-kernel och importera sedan Theano.  <br/>För att undvika ett tidigare MKL-fel (math kernel Library) måste du först ställa in MKL-tråd skiktet på följande sätt:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |Nej | Ja |Chainer är installerat i [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL och ChainerCV är installerade. <br/><br/>Exempelanteckningsböcker som ingår i JupyterHub.<br/><br/>**Så här kör du det**: <br/>Slutliga Aktivera [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -miljön, kör _python_och importera sedan en kedja. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och gå sedan till katalogen kedjar för att hitta exempel antecknings böcker.|
|[NVidia-siffror](https://github.com/NVIDIA/DIGITS) | Nej | Ja (Ubuntu) |Djup inlärnings system från NVIDIA för snabb inlärning av djup inlärnings modeller. Siffror installeras i `/dsvm/tools/DIGITS` och är tillgängligt som en tjänst med namnet _siffror_.  <br/><br/>**Så här kör du det**: <br/>Logga in på den virtuella datorn med X2Go. Starta tjänsten genom att köra ```sudo systemctl start digits```i en Terminal. <br/><br/>Tjänsten tar ungefär en minut för att starta. Öppna en webbläsare och gå till `http://localhost:5000`. Observera att SIFFROR ger inte en säker inloggning och bör inte exponeras utanför den virtuella datorn.|
|[CUDA, cuDNN, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit) |Ja | Ja | |
|NVIDIA smi|Ja | Ja |NVIDIA-verktyg för att fråga GPU-aktivitet; _NVIDIA-SMI_ är tillgängligt på system Sök vägen. <br/><br/>Öppna en kommando tolk (i Windows) eller en Terminal (på Linux) och kör sedan _NVIDIA-SMI_.|
|[TensorFlow-servar](https://www.tensorflow.org/serving/) | Nej | Ja |En server som ska utformas för en TensorFlow modell. tensorflow_model_server är tillgänglig i terminalen. Exempel finns [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nej | Ja (Ubuntu) |En server för djup inlärnings störningar från NVIDIA. TensorRT installeras som en _apt_ paketet. Exempel finns [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Ja | Ja | Installeras i python 3,5 på [Linux och Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Exempel på Jupyter-anteckningsböcker ingår på DSVM. <br/><br/>**Så här kör du det**: <br/>Slutliga Aktivera rätt miljö och kör python. <br/>Jupyter Anslut till [Jupyter](provision-vm.md) eller [JUPYTERHUB](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och öppna CNTK-katalogen för exempel. |
|Djupgående Water|Nej | Ja (Ubuntu) |Ramverk för djup inlärning för H2O, djup vatten är installerat i [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) och finns också i `/dsvm/tools/deep_water`. Exempelanteckningsböcker som ingår i JupyterHub. Djupgående Water kräver CUDA 8 med cuDNN 5.1. Som standard finns detta inte i biblioteks Sök vägen, eftersom andra ramverk för djup inlärning använder CUDA 9 och cuDNN 7. Om du vill använda installerar du CUDA 8 + cuDNN 5,1 för djup vatten:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Använda djup Water:<br/>Slutliga Aktivera [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -miljön och kör sedan _python_. <br/>* JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)och gå sedan till katalogen deep_water för att hitta exempel antecknings böcker.|
