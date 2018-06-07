---
title: Kontrollera Azure resursanvändningen mot gränser | Microsoft Docs
description: Lär dig hur du kontrollerar din Azure Resursanvändning mot Azure-prenumerationsbegränsningar.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: networking
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 0c51f48576c665fbe67f2f18198d6422fe872895
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811748"
---
# <a name="check-resource-usage-against-limits"></a>Kontrollera resursanvändningen mot gränser

I den här artikeln beskrivs hur du se hur många av varje typ av resurs som du har distribuerat i din prenumeration och vad din [prenumerationsbegränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) är. Möjligheten att visa resursanvändningen mot gränser är bra att spåra aktuell användning och planera för framtida användning. Du kan använda den [Azure Portal](#azure-portal), [PowerShell](#powershell), eller [Azure CLI](#azure-cli) spåra användningen.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på Azure [portal](https://portal.azure.com).
2. I det övre, vänstra hörnet på Azure portal väljer **alla tjänster**.
3. Ange *prenumerationer* i den **Filter** rutan. När **prenumerationer** visas i sökresultaten väljer den.
4. Välj namnet på den prenumeration som du vill visa information om användning av.
5. Under **inställningar**väljer **användning + kvoten**.
6. Du kan välja följande alternativ:
    - **Resurstyper**: du kan välja alla typer av resurser eller Välj vilken typ av resurser som du vill visa.
    - **Providers**: du kan välja alla providrar eller välj **Compute**, **nätverk**, eller **lagring**.
    - **Platser**: du kan välja alla Azure platser eller välja specifika platser.
    - Du kan välja för att visa alla resurser eller endast resurser där minst en distribueras.

    Exemplet i följande bild visar alla nätverksresurser med minst en resurs som har distribuerats i östra USA:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    Du kan sortera kolumner genom att välja kolumnrubriken. De gränser som visas är gränsvärdena för din prenumeration. Om du behöver öka en standardgräns väljer **begäran öka**, slutföra och skicka in supportbegäran. Alla resurser som har en maxgräns som anges i Azure [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Om din aktuella gränsen är redan det maximala antalet, går inte att öka gränsen.

## <a name="powershell"></a>PowerShell

Du kan köra kommandon som visas i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud-gränssnittet är ett kostnadsfritt interaktiva gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modul, version 6.0.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt, måste du också köra `Login-AzureRmAccount` att logga in på Azure.

Visa din användning mot gränser med [Get-AzureRmNetworkUsage](/powershell/module/azurerm.network/powershell/module/azurerm.network/get-azurermnetworkusage). I följande exempel hämtas användning för resurser där minst en resurs har distribuerats på östra USA plats:

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Visas utdata formateras samma som i följande exempel på utdata:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt kan du också behöva köra `az login` att logga in på Azure.

Visa din användning mot gränser med [az nätverket lista-användningsområden](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). I följande exempel hämtar förbrukning av resurser i östra USA plats:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Visas utdata formateras samma som i följande exempel på utdata:

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
