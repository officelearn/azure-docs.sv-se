---
title: Använd en brand vägg
titleSuffix: Azure Machine Learning
description: Kontrol lera åtkomsten till Azure Machine Learning arbets ytor med Azure-brandväggar. Läs om de värdar som du måste tillåta genom brand väggen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 5ba1804630649044d29dd2919ef8375265a69a08
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320883"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Använd arbets ytan bakom en brand vägg för Azure Machine Learning

I den här artikeln lär du dig hur du konfigurerar Azure-brandväggen för att kontrol lera åtkomsten till din Azure Machine Learning-arbetsyta och det offentliga Internet.   Mer information om hur du skyddar Azure Machine Learning finns i [företags säkerhet för Azure Machine Learning](concept-enterprise-security.md)

Informationen i det här dokumentet baseras på användningen av [Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md), men du bör kunna använda den med andra brand Väggs produkter. Om du har frågor om hur du tillåter kommunikation via brand väggen kan du läsa dokumentationen för den brand vägg som du använder.

## <a name="application-rules"></a>Programregler

I brand väggen skapar du en _program regel_ som tillåter trafik till och från adresserna i den här artikeln.

> [!TIP]
> När du lägger till nätverks regeln ställer du in __protokollet__ på valfri, och portarna till `*` .
>
> Mer information om hur du konfigurerar Azure-brandväggen finns i [distribuera och konfigurera Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="routes"></a>Vägar

När du konfigurerar den utgående vägen för under nätet som innehåller Azure Machine Learning resurser använder du rikt linjerna i avsnittet [Tvingad tunnel trafik](how-to-secure-training-vnet.md#forced-tunneling) för att skydda inlärnings miljön.

## <a name="microsoft-hosts"></a>Microsoft-värdar

Om konfigurationen inte är korrekt konfigurerad kan brand väggen orsaka problem med din arbets yta. Det finns flera olika värdnamn som används både i Azure Machine Learning-arbetsytan.

Värdarna i det här avsnittet ägs av Microsoft och tillhandahåller tjänster som krävs för att din arbets yta ska fungera korrekt.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **login.microsoftonline.com** | Autentisering |
| **management.azure.com** | Används för att hämta information om arbets ytan |
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
| **\*. notebooks.azure.net** | Krävs av antecknings böckerna i Azure Machine Learning Studio. |
| **\*. file.core.windows.net** | Krävs av Utforskaren i Azure Machine Learning Studio. |
| **\*. dfs.core.windows.net** | Krävs av Utforskaren i Azure Machine Learning Studio. |
| **graph.windows.net** | Krävs för antecknings böcker |

> [!TIP]
> Om du planerar att använda federerade identiteter följer du [metod tips för att skydda Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) artikel.

## <a name="python-hosts"></a>Python-värdar

Värdarna i det här avsnittet används för att installera python-paket. De krävs vid utveckling, utbildning och distribution. 

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Används för att installera standard paket. |
| **\*. anaconda.org** | Används för att hämta lagrings platsen-data. |
| **pypi.org** | Används för att Visa beroenden från standard index, om det finns några, och indexet skrivs inte över av användar inställningarna. Om indexet skrivs över måste du också tillåta **\* . pythonhosted.org**. |

## <a name="r-hosts"></a>R-värdar

Värdarna i det här avsnittet används för att installera R-paket. De krävs vid utveckling, utbildning och distribution.

> [!IMPORTANT]
> R SDK för Azure Machine Learning använder python-paket internt. Så du måste också tillåta python-värdar genom brand väggen.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **cloud.r-project.org** | Används vid installation av CRAN-paket. |

## <a name="azure-government-region"></a>Azure Government Region

URL: er som krävs för Azure Government regionerna.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | US-Arizona region |
| **usgovvirginia.api.ml.azure.us** | US-Virginia region |

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Skydda Azure ML-experimenterings- och slutsatsdragningsjobb i ett virtuellt Azure-nätverk](how-to-network-security-overview.md)
