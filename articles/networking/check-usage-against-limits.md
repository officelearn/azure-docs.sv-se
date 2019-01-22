---
title: Kontrollera Azure-Resursanvändning mot gränser | Microsoft Docs
description: Lär dig hur du kontrollerar din Azure-Resursanvändning mot Azure-prenumerationsbegränsningar.
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
ms.openlocfilehash: 0b15861f663c98d3b873f95a0ea6c485ada91fb6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421614"
---
# <a name="check-resource-usage-against-limits"></a>Kontrollera resursanvändningen mot gränser

I den här artikeln du lär dig att se hur många av varje typ av resurs som du har distribuerat i din prenumeration och vad din [prenumerationsbegränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) är. Möjligheten att visa Resursanvändning mot gränser är bra att spåra aktuell användning och planera för framtida användning. Du kan använda den [Azure-portalen](#azure-portal), [PowerShell](#powershell), eller [Azure CLI](#azure-cli) att spåra användningen.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på Azure [portal](https://portal.azure.com).
2. I det övre, vänstra hörnet i Azure Portal, Välj **alla tjänster**.
3. Ange *prenumerationer* i den **Filter** box. När **prenumerationer** visas i sökresultatet väljer du det.
4. Välj namnet på den prenumeration som du vill visa användningsinformation för.
5. Under **inställningar**väljer **användning + kvoter**.
6. Du kan välja följande alternativ:
    - **Resurstyper**: Du kan välja alla typer av resurser eller Välj vilken typ av resurser som du vill visa.
    - **Providers**: Du kan välja alla resursprovidrar eller välj **Compute**, **nätverk**, eller **Storage**.
    - **Platser**: Du kan välja alla Azure-platser eller välja specifika platser.
    - Du kan välja för att visa alla resurser eller de resurser där minst en distribueras.

    Exempel på följande bild visar alla nätverksresurser med minst en resurs som är distribuerad i östra USA:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    Du kan sortera kolumner genom att välja kolumnens rubrik. Gränserna som visas är gränserna för din prenumeration. Om du vill utöka en standardgräns väljer **begäran öka**, Slutför och skicka in supportbegäran. Alla resurser har en maxgräns som visas i Azure [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Om din aktuella gränsen är redan det maximala antalet, kan inte gränsen ökas.

## <a name="powershell"></a>PowerShell

Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modulen version 6.0.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn, hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt, måste du också köra `Login-AzureRmAccount` att logga in på Azure.

Visa din användning mot gränser med [Get-AzureRmNetworkUsage](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.8.0). I följande exempel hämtas användningen av resurser där minst en resurs är distribuerad i platsen East USA:

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Du får utdata formaterade samma som följande Exempelutdata:

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

Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt kan du också behöva köra `az login` att logga in på Azure.

Visa din användning mot gränser med [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). I följande exempel hämtar förbrukning av resurser i platsen East USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Du får utdata formaterade samma som följande Exempelutdata:

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
