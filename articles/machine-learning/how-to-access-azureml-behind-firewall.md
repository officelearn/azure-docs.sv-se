---
title: Använd en brand vägg
titleSuffix: Azure Machine Learning
description: Kontrol lera åtkomsten till Azure Machine Learning arbets ytor med Azure-brandväggar. Läs om de värdar som du måste tillåta genom brand väggen för att Azure Machine Learning ska fungera korrekt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 27b625dfa31b366d95922e1dd0bad7fda6e86ed4
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540078"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Använd arbets ytan bakom en brand vägg för Azure Machine Learning

I den här artikeln lär du dig hur du konfigurerar Azure-brandväggen för användning med en Azure Machine Learning-arbetsyta.

> [!IMPORTANT]
> Informationen i det här dokumentet baseras på användningen av Azure-brandväggen, men du bör kunna använda den med andra brand Väggs produkter. Om du har frågor om hur du tillåter kommunikation via brand väggen kan du läsa dokumentationen för den brand vägg som du använder.

Azure-brandväggen kan användas för att styra åtkomsten till din Azure Machine Learning-arbetsyta och det offentliga Internet. Om konfigurationen inte är korrekt konfigurerad kan brand väggen orsaka problem med din arbets yta. Det finns flera olika värdnamn som används både i Azure Machine Learning-arbetsytan, som beskrivs i den här artikeln.

## <a name="network-rules"></a>Nätverksregler

I brand väggen skapar du en nätverks regel som tillåter trafik till och från adresserna i den här artikeln.

> [!TIP]
> När du lägger till nätverks regeln ställer du in __protokollet__ på valfri, och portarna till `*` .
>
> Mer information om hur du konfigurerar Azure-brandväggen finns i [distribuera och konfigurera Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Microsoft-värdar

Värdarna i det här avsnittet ägs av Microsoft och tillhandahåller tjänster som krävs för att din arbets yta ska fungera korrekt.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Tränings kluster |
| **ml.azure.com** | Azure Machine Learning-studio |
| **default.exp-tas.com** | Används av Azure Machine Learning Studio |
| **\*. azureml.ms** | Används av Azure Machine Learning API: er |
| **\*. experiments.azureml.net** | Används av experiment som körs i Azure Machine Learning |
| **\*. modelmanagement.azureml.net** | Används för att registrera och distribuera modeller|
| **mlworkspace.azure.ai** | Används av Azure Portal när du visar en arbets yta |
| **\*. aether.ms** | Används när Azure Machine Learning pipeliner körs |
| **\*. instances.azureml.net** | Azure Machine Learning beräknings instanser |
| **\*. instances.azureml.ms** | Azure Machine Learning beräknings instanser när privat länk har Aktiver ATS för arbets ytan |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Microsoft Container Registry för Base Docker-avbildningar |
| **your-acr-server-name.azurecr.io** | Krävs endast om din Azure Container Registry ligger bakom det virtuella nätverket. I den här konfigurationen skapas en privat länk från Microsoft-miljön till ACR-instansen i din prenumeration. Använd namnet på ACR-servern för din Azure Machine Learning-arbetsyta. |

## <a name="python-hosts"></a>Python-värdar

Värdarna i det här avsnittet används för att installera python-paket. De krävs vid utveckling, utbildning och distribution. 

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **anaconda.com** | Används för att installera standard paket. |
| **\*. anaconda.org** | Används för att hämta lagrings platsen-data. |
| **pypi.org** | Används för att Visa beroenden från standard index, om det finns några, och indexet skrivs inte över av användar inställningarna. Om indexet skrivs över måste du också tillåta ** \* . pythonhosted.org**. |

## <a name="r-hosts"></a>R-värdar

Värdarna i det här avsnittet används för att installera R-paket. De krävs vid utveckling, utbildning och distribution.

> [!IMPORTANT]
> R SDK för Azure Machine Learning använder python-paket internt. Så du måste också tillåta python-värdar genom brand väggen.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **cloud.r-project.org** | Används vid installation av CRAN-paket. |

Nästa steg

* [[Distribuera och konfigurera Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)]
* [Skydda Azure ML-experimentering och härlednings jobb i en Azure-Virtual Network](how-to-enable-virtual-network.md)
