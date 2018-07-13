---
title: Aktivera den offentliga förhandsversionen av Azure-brandvägg
description: Använd Azure PowerShell för att aktivera den offentliga förhandsversionen av Azure-brandvägg
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991455"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Aktivera den offentliga förhandsversionen av Azure-brandvägg

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Aktivera med hjälp av Azure PowerShell

Om du vill aktivera den offentliga förhandsversionen av Azure-brandväggen, använder du följande Azure PowerShell-kommandon:

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Det tar upp till 30 minuter för funktionsregistrering av att slutföra. Du kan kontrollera din registreringsstatus som genom att köra följande Azure PowerShell-kommandon:

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
När registreringen är klar kör du följande kommando:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure-brandvägg med hjälp av Azure portal](tutorial-firewall-deploy-portal.md)

