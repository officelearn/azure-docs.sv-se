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
ms.date: 04/27/2020
ms.openlocfilehash: 40c25dda3fefa9c54df832e16149a68a4aa5a33b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981973"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Använd arbets ytan bakom Azure-brandväggen för Azure Machine Learning

I den här artikeln lär du dig hur du konfigurerar Azure-brandväggen för användning med en Azure Machine Learning-arbetsyta.

Azure-brandväggen kan användas för att styra åtkomsten till din Azure Machine Learning-arbetsyta och det offentliga Internet. Om konfigurationen inte är korrekt konfigurerad kan brand väggen orsaka problem med din arbets yta.

## <a name="network-rules"></a>Nätverksregler

I brand väggen skapar du en nätverks regel som tillåter trafik till och från adresserna i den här artikeln.

> [!TIP]
> När du lägger till nätverks regeln ställer du in __protokollet__ på valfri, och portarna `*`till.
>
> Mer information om hur du konfigurerar Azure-brandväggen finns i [distribuera och konfigurera Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Microsoft-värdar

Värdarna i det här avsnittet ägs av Microsoft och tillhandahåller tjänster som krävs för att din arbets yta ska fungera korrekt.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Tränings kluster |
| **ml.azure.com** | Azure Machine Learning-studio |
| **\*. azureml.ms** | Används av Azure Machine Learning API: er |
| **\*. experiments.azureml.net** | Används av experiment som körs i Azure Machine Learning|
| **\*. modelmanagement.azureml.net** | Används för att registrera och distribuera modeller|
| **mlworkspace.azure.ai** | Används av Azure Portal när du visar en arbets yta |
| **\*. aether.ms** | Används när Azure Machine Learning pipeliner körs |
| **\*. instances.azureml.net** | Azure Machine Learning beräknings instanser |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | Grundläggande Docker-avbildningar |
| **azurecr.io** | Azure Container Registry |

## <a name="python-hosts"></a>Python-värdar

Värdarna i det här avsnittet används för att installera python-paket. De krävs vid utveckling, utbildning och distribution. 

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **anaconda.com** | Används vid installation av Conda-paket |
| **pypi.org** | Används vid installation av pip-paket |

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
