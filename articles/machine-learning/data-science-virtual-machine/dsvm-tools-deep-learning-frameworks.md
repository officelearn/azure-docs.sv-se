---
title: Djup inlärning & AI-ramverk
titleSuffix: Azure Data Science Virtual Machine
description: Tillgängliga ramverk för djup inlärning och verktyg på Azure Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: tracking-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 25ce99d3dced3caf1ec4bcce13b062b28774642d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84557488"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Djup inlärnings-och AI-ramverk för Azure-Data Science VM
Ramverk för djup inlärning i DSVM visas nedan.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | |
| DSVM-versioner som stöds      | Linux (Ubuntu)     |
| Hur konfigureras den/installeras på DSVM?  | Caffe installeras i `/opt/caffe` .   Exempel finns i `/opt/caffe/examples` .|
| Så här kör du det      | Använd X2Go för att logga in på den virtuella datorn och starta sedan en ny terminal och ange följande:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Ett nytt webbläsarfönster öppnas med exempel antecknings böcker. Binärfiler installeras i/opt/Caffe/build/install/bin.<br/><br/>Den installerade versionen av Caffe kräver python 2,7 och fungerar inte med python 3,5, som aktive ras som standard. Om du vill växla till python 2,7 kör `source activate root` du för att växla till Anaconda-miljön.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | |
| DSVM-versioner som stöds      | Linux (Ubuntu)     |
| Hur konfigureras den/installeras på DSVM?  | Caffe2 installeras i Conda-miljön [python 2,7 (root). |
| Så här kör du det      | Terminal: starta python och importera Caffe2. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och gå sedan till Caffe2-katalogen för att hitta exempel antecknings böcker. Vissa antecknings böcker kräver att Caffe2-roten anges i python-koden. Ange/opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 5.2 |
| DSVM-versioner som stöds      | Linux (Ubuntu)     |
| Hur konfigureras den/installeras på DSVM?  | Kedjor installeras i python 3,5. |
| Så här kör du det      | Terminal: Aktivera python 3,5-miljön, kör `python` och sedan `import chainer` . <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och gå sedan till kedje katalogen för att hitta exempel antecknings böcker.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA-, cuDNN-, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 10.0.130|
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur konfigureras den/installeras på DSVM?  |_NVIDIA-SMI_ är tillgängligt på system Sök vägen.  |
| Så här kör du det      | Öppna en kommando tolk (i Windows) eller en Terminal (på Linux) och kör sedan _NVIDIA-SMI_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 0.16.1|
| DSVM-versioner som stöds      | Linux (Ubuntu)   |
| Hur konfigureras den/installeras på DSVM?  | Horovod installeras i python 3,5 |
| Så här kör du det      | Aktivera rätt miljö vid terminalen och kör sedan python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 2.2.4 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur konfigureras den/installeras på DSVM?  | Keras installeras i python 3,6 på Windows och i python 3,5 i Linux |
| Så här kör du det      | Aktivera rätt miljö vid terminalen och kör sedan python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 2.5.1 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur konfigureras den/installeras på DSVM?  | CNTK installeras i python 3,6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) och i python 3,5 på [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Så här kör du det      | Terminal: aktivera rätt miljö och kör python. <br/>Jupyter: Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna sedan CNTK-katalogen för exempel. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.3.0 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur konfigureras den/installeras på DSVM?  | MXNet installeras i `C:\dsvm\tools\mxnet` på Windows och `/dsvm/tools/mxnet` på Ubuntu. Python-bindningar installeras i python 3,6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) och i python 3,5 på [Linux](./dsvm-tools-languages.md#python-linux-edition)) R-bindningar ingår också i Ubuntu-DSVM. |
| Så här kör du det      | Terminal: aktivera rätt Conda-miljö och kör sedan `import mxnet` . <br/>Jupyter: Anslut till [Jupyter](provision-vm.md#access-the-dsvm) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna sedan `mxnet` katalogen för exempel. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.0.1 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur konfigureras den/installeras på DSVM?  | MXNet modell Server installeras i python 3,6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) och i python 3,5 på [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Så här kör du det      | Terminal: kör `sudo systemctl stop jupyterhub` för att stoppa JupyterHub-tjänsten först, eftersom båda lyssnar på samma port. Aktivera sedan rätt Conda-miljö och kör`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVIDIA-gränssnitt för system hantering (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds |  |
| DSVM-versioner som stöds      | Windows och Linux   |
| Vad är det för? | NVIDIA-verktyg för att fråga GPU-aktivitet |
| Hur konfigureras den/installeras på DSVM?  | `nvidia-smi`finns på system Sök vägen. |
| Så här kör du det      | Öppna en kommando tolk (i Windows) eller en Terminal (på Linux) på en virtuell dator **med GPU: n**och kör sedan `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.2.0 (Ubuntu 16,04, Windows 2016), 1.4.0 (Ubuntu 18,04, Windows 2019) |
| DSVM-versioner som stöds      | Linux |
| Hur konfigureras den/installeras på DSVM?  | Installerat i [Python 3,5](dsvm-tools-languages.md#python-linux-edition). Exempel på Jupyter-anteckningsböcker ingår och exempel finns i/dsvm/samples/pytorch. |
| Så här kör du det      | Terminal: aktivera rätt miljö och kör sedan python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Anslut och öppna katalogen PyTorch för exempel.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.13 |
| DSVM-versioner som stöds      | Windows, Linux |
| Hur konfigureras den/installeras på DSVM?  | Installerat i python 3,5 på [Linux](dsvm-tools-languages.md#python-linux-edition) och python 3,6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Så här kör du det      | Terminal: aktivera rätt miljö och kör sedan python. <br/> * Jupyter: Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna sedan TensorFlow-katalogen för exempel.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.12 |
| DSVM-versioner som stöds      | Linux |
| Hur konfigureras den/installeras på DSVM?  | tensorflow_model_server är tillgängligt på terminalen. |
| Så här kör du det      |  Exempel är tillgängliga [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.0.3 |
| DSVM-versioner som stöds      | Linux |
| Hur konfigureras den/installeras på DSVM?  |Theano installeras i python 2,7 (_root_) och i python 3,5 (_py35_)-miljö. |
| Så här kör du det      |  Terminal: Aktivera den python-version som du vill använda (root eller py35), kör python och importera sedan Theano.<br/>* Jupyter: Välj python 2,7-eller 3,5-kernel och importera sedan Theano.  <br/>För att undvika ett tidigare MKL-fel (math kernel Library) måste du först ställa in MKL-tråd skiktet på följande sätt:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
