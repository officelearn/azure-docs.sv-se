---
title: Kontrollera Azure-resursanvändning mot gränser | Microsoft-dokument
description: Lär dig hur du kontrollerar din Azure-resursanvändning mot Azure-prenumerationsgränser.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: cffa5677c5531f3887639c049998523d7d07586a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455570"
---
# <a name="check-resource-usage-against-limits"></a>Kontrollera resursanvändningen mot gränser

I den här artikeln får du lära dig hur du ser antalet nätverksresurser som du har distribuerat i prenumerationen och vilka [dina prenumerationsgränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) är. Möjligheten att visa resursanvändning mot gränser är användbart för att spåra aktuell användning och planera för framtida användning. Du kan använda [Azure Portal,](#azure-portal) [PowerShell](#powershell)eller [Azure CLI](#azure-cli) för att spåra användning.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Högst upp, till vänster i Azure-portalen, väljer du **Alla tjänster**.
3. Ange *prenumerationer* i rutan **Filter.** När **Prenumerationer** visas bland sökresultaten väljer du posten.
4. Välj namnet på den prenumeration som du vill visa användningsinformation för.
5. Under **INSTÄLLNINGAR**väljer du **Användning + kvot**.
6. Följande alternativ finns:
   - **Resurstyper:** Du kan markera alla resurstyper eller välja de specifika typer av resurser som du vill visa.
   - **Leverantörer:** Du kan välja alla resursleverantörer eller välja **Beräkna,** **Nätverk**eller **Lagring**.
   - **Platser**: Du kan välja alla Azure-platser eller välja specifika platser.
   - Du kan välja att visa alla resurser, eller bara de resurser där minst en distribueras.

     I exemplet i följande bild visas alla nätverksresurser med minst en resurs som distribueras i östra USA:

       ![Visa användningsdata](./media/check-usage-against-limits/view-usage.png)

     Du kan sortera kolumnerna genom att markera kolumnrubriken. De gränser som visas är gränserna för din prenumeration. Om du behöver öka en standardgräns väljer du **Begär ökning**och slutför och skickar supportbegäran. Alla resurser har en maximal gräns som anges i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Om den aktuella gränsen redan har det maximala antalet kan gränsen inte höjas.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Login-AzAccount` också köra för att logga in på Azure.

Visa din användning mot gränser med [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). I följande exempel hämtars användningen för resurser där minst en resurs distribueras på platsen i östra USA:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Du får utdata som är formaterade på samma sätt som följande exempelutdata:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att logga in på Azure.

Visa din användning mot gränser med [az-nätverkslistanvändningar](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). I följande exempel hämtas användningen för resurser på platsen i östra USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Du får utdata som är formaterade på samma sätt som följande exempelutdata:

```output
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
