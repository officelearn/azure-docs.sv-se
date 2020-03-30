---
title: Deep Learning & AI-ramverk
titleSuffix: Azure Data Science Virtual Machine
description: Tillgängliga ramverk och verktyg för djupinlärning på Virtual Machine för Azure Data Science.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270074"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Djupinlärnings- och AI-ramverk för den virtuella virtuella Azure Data Science
Deep learning-ramverk på DSVM listas nedan.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | |
| DSVM-utgåvor som stöds      | Linux (Ubuntu)     |
| Hur är det konfigurerat / installerat på DSVM?  | Caffe är `/opt/caffe`installerat i .   Proverna `/opt/caffe/examples`finns i .|
| Hur man kör det      | Använd X2Go för att logga in på den virtuella datorn och starta sedan en ny terminal och ange följande:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Ett nytt webbläsarfönster öppnas med exempeldatorböcker. Binärfiler installeras i /opt/caffe/build/install/bin.<br/><br/>Installerad version av Caffe kräver Python 2.7 och fungerar inte med Python 3.5, som aktiveras som standard. Om du vill växla till Python `source activate root` 2.7 kör du för att växla till Anaconda-miljö.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | |
| DSVM-utgåvor som stöds      | Linux (Ubuntu)     |
| Hur är det konfigurerat / installerat på DSVM?  | Caffe2 installeras i [Python 2.7 (root) conda-miljön. |
| Hur man kör det      | Terminal: Starta Python och importera Caffe2. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), och sedan gå till Caffe2 katalogen för att hitta exempel anteckningsböcker. Vissa anteckningsböcker kräver att Caffe2-roten anges i Python-koden. /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 5.2 |
| DSVM-utgåvor som stöds      | Linux (Ubuntu)     |
| Hur är det konfigurerat / installerat på DSVM?  | Chainer installeras i Python 3.5. |
| Hur man kör det      | Terminal: Aktivera Python 3.5-miljön, `python` `import chainer`kör och sedan . <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), och sedan gå till Chainer katalogen för att hitta exempel anteckningsböcker.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA-, cuDNN-, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 10.0.130|
| DSVM-utgåvor som stöds      | Windows och Linux   |
| Hur är det konfigurerat / installerat på DSVM?  |_nvidia-smi_ finns på systemvägen.  |
| Hur man kör det      | Öppna en kommandotolk (i Windows) eller en terminal (på Linux) och kör sedan _nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 0.16.1|
| DSVM-utgåvor som stöds      | Linux (Ubuntu)   |
| Hur är det konfigurerat / installerat på DSVM?  | Horovod installeras i Python 3.5 |
| Hur man kör det      | Aktivera rätt miljö vid terminalen och kör sedan Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 2.2.4 |
| DSVM-utgåvor som stöds      | Windows och Linux   |
| Hur är det konfigurerat / installerat på DSVM?  | Keras installeras i Python 3.6 på Windows och i Python 3.5 i Linux |
| Hur man kör det      | Aktivera rätt miljö vid terminalen och kör sedan Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 2.5.1 |
| DSVM-utgåvor som stöds      | Windows och Linux   |
| Hur är det konfigurerat / installerat på DSVM?  | CNTK installeras i Python 3.6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) och i Python 3.5 på [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Hur man kör det      | Terminal: Aktivera rätt miljö och kör Python. <br/>Jupyter: Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna sedan CNTK-katalogen för exempel. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Versioner som stöds | 1.3.0 |
| DSVM-utgåvor som stöds      | Windows och Linux   |
| Hur är det konfigurerat / installerat på DSVM?  | MXNet är `C:\dsvm\tools\mxnet` installerat i `/dsvm/tools/mxnet` windows och på Ubuntu. Python-bindningar installeras i Python 3.6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) och i Python 3.5 på [Linux](./dsvm-tools-languages.md#python-linux-edition)) R-bindningar ingår också i Ubuntu DSVM. |
| Hur man kör det      | Terminal: Aktivera rätt conda-miljö `import mxnet`och kör sedan . <br/>Jupyter: Anslut till [Jupyter](provision-vm.md#access-the-dsvm) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), och öppna sedan katalogen `mxnet` för exempel. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 1.0.1 |
| DSVM-utgåvor som stöds      | Windows och Linux   |
| Hur är det konfigurerat / installerat på DSVM?  | MXNet Model Server installeras i Python 3.6 på [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) och i Python 3.5 på [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Hur man kör det      | Terminal: `sudo systemctl stop jupyterhub` Kör för att stoppa JupyterHub-tjänsten först, eftersom båda lyssnar på samma port. Aktivera sedan rätt conda-miljö och kör`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Versioner som stöds |  |
| DSVM-utgåvor som stöds      | Windows och Linux   |
| Vad är den till för? | NVIDIA-verktyg för att fråga GPU-aktivitet |
| Hur är det konfigurerat / installerat på DSVM?  | `nvidia-smi`är på systemsökvägen. |
| Hur man kör det      | Öppna en kommandotolk (på Windows) eller en terminal (på Linux) på `nvidia-smi`en virtuell dator **med GPU:er**och kör sedan . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| DSVM-utgåvor som stöds      | Linux |
| Hur är det konfigurerat / installerat på DSVM?  | Installerad i [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Prov Jupyter bärbara datorer ingår, och prover finns i / dsvm / prover / pytorch. |
| Hur man kör det      | Terminal: Aktivera rätt miljö och kör sedan Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Anslut och öppna sedan PyTorch-katalogen för exempel.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 1.13 |
| DSVM-utgåvor som stöds      | Windows, Linux |
| Hur är det konfigurerat / installerat på DSVM?  | Installerad i Python 3.5 på [Linux](dsvm-tools-languages.md#python-linux-edition) och Python 3.6 i [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Hur man kör det      | Terminal: Aktivera rätt miljö och kör sedan Python. <br/> * Jupyter: Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), och sedan öppna TensorFlow katalogen för prover.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 1.12 |
| DSVM-utgåvor som stöds      | Linux |
| Hur är det konfigurerat / installerat på DSVM?  | tensorflow_model_server finns i terminalen. |
| Hur man kör det      |  Prover finns tillgängliga [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Versioner som stöds | 1.0.3 |
| DSVM-utgåvor som stöds      | Linux |
| Hur är det konfigurerat / installerat på DSVM?  |Theano installeras i Python 2.7 (_root_) och i Python 3.5 (_py35_) miljö. |
| Hur man kör det      |  Terminal: Aktivera den Python-version du vill ha (root eller py35), kör Python och importera sedan Theano.<br/>* Jupyter: Välj Python 2.7 eller 3.5 kärna, och sedan importera Theano.  <br/>För att komma runt en nyligen matematiska kernel bibliotek (MKL) bugg, måste du först ställa in MKL trådskikt enligt följande:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |