---
title: Skapa, ändra eller ta bort ett Azure offentlig IP-adressprefix | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort en offentlig IP-adressprefix.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 71dee9f36a3e8e11cc23f966c9157f6409a3b5e0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405741"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Skapa, ändra eller ta bort en offentlig IP-adressprefix

Läs om en offentlig IP-adressprefix och hur du skapar, ändra och ta bort ett. En offentlig IP-adressprefixet är ett sammanhängande intervall av adresser baserat på antalet offentliga IP-adresser som du anger. Adresserna som har tilldelats till din prenumeration. När du skapar en offentlig IP-adressresurs kan du tilldela en statisk offentlig IP-adress från prefixet och associera till virtuella datorer, läsa in belastningsutjämnare eller andra resurser för Internetanslutning. Om du inte är bekant med offentliga IP-adressprefix, se [offentliga IP-adress prefix översikt](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Innan du börjar

> [!IMPORTANT]
> Offentliga IP-Prefix är i en offentlig förhandsversion i begränsade regioner. Du kan [Lär dig vad det innebär att finnas i förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Offentliga IP-prefix är för närvarande tillgängligt i: västra centrala USA, västra USA, västra USA 2, centrala USA, Nordeuropa, Västeuropa och Sydostasien. En uppdaterad lista över regioner finns [Azure-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://aka.ms/publicipprefixportal, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver AzureRm.Network PowerShell-Modulversion 6.3.1 eller senare. Kör `Get-Module -ListAvailable AzureRM.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

Offentliga IP-adressprefix har en avgift. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Skapa en offentlig IP-adressprefix

1. I det övre, vänstra hörnet i portalen, Välj **+ skapa en resurs**.
2. Ange *offentliga ip-adressprefix* i den *Sök på Marketplace* box. När **offentliga IP-adressprefix** visas i sökresultatet väljer du det.
3. Under **offentliga IP-adressprefix**väljer **skapa**.
4. Ange eller Välj värden för följande inställningar under **skapa offentlig IP-adressprefix**och välj sedan **skapa**:

   |Inställning|Krävs?|Information|
   |---|---|---|
   |Prenumeration|Ja|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som du vill associera den offentliga IP-adressen till resursen.|
   |Resursgrupp|Ja|Kan finnas i samma eller olika, [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som du vill associera den offentliga IP-adressen till resursen.|
   |Namn|Ja|Namnet måste vara unikt inom den resursgrupp som du väljer.|
   |Region|Ja|Måste finnas i samma [region](https://azure.microsoft.com/regions)som offentliga IP-adresser ska du tilldela adresser i intervallet. Prefixet är för närvarande är förhandsversion i västra centrala USA, västra USA, västra USA 2, centrala USA, Nordeuropa, Västeuropa och Sydostasien.|
   |Prefixstorlek|Ja| Storleken på det prefix som du behöver. A/28 eller 16 IP-adresser är standard. 

**Kommandon**


|Verktyg|Kommando|
|---|---|
|CLI|[Skapa AZ nätverket offentliga ip-prefix](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-create)|
|PowerShell|[Ny AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/new-azurermpublicipaddressprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Skapa en statisk offentlig IP-adress från ett prefix
När du skapar ett prefix, måste du skapa statiska IP-adresser från prefixet. Följ stegen nedan för att göra detta.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *offentliga ip-adressprefix*. När **offentliga IP-adressprefixen** visas i sökresultaten, markerar du den.
2. Välj prefx som du vill skapa offentliga IP-adresser från.
3. När den visas i sökresultatet väljer du det och klickar på **+ Lägg till IP-adress** i översiktsavsnittet. Om du inte ser det här kan du kontrollera att du använder rätt länken för förhandsversion: https://aka.ms/publicipprefixportal
4. Ange eller Välj värden för följande inställningar under **skapa offentlig IP-adress**. Eftersom det är ett prefix för Standard-SKU, IPv4 och statiska, behöver du bara ange följande informtion:

   |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Namnet på den offentliga IP-adressen måste vara unikt inom den resursgrupp som du väljer.|
   |Timeout för inaktivitet (minuter)|Nej|Hur många minuter att hålla en TCP eller HTTP-anslutning öppen utan att behöva klienter skickar keep-alive-meddelanden. |
   |DNS-namnetikett|Nej|Måste vara unikt inom Azure-region du skapar namnet i (över alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i dess DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät som *location.cloudapp.azure.com* (där platsen är platsen du väljer) till namnet du anger, för att skapa det fullständigt kvalificerade DNS-namnet. Mer information finns i [Använd Azure DNS med Azure offentlig IP-adressen](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

## <a name="view-or-delete-a-prefix"></a>Visa eller ta bort ett prefix

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *offentliga ip-adressprefix*. När **offentliga IP-adressprefixen** visas i sökresultaten, markerar du den.
2. Välj namnet på den offentliga IP-adressprefix som du vill visa, ändra inställningar för eller ta bort från listan.
3. Gör något av följande alternativ, beroende på om du vill visa, ta bort eller ändra det offentliga IP-adressprefixet.
    - **Visa**: den **översikt** visar inställningar för offentliga IP-adressprefix, till exempel prefix.
    - **Ta bort**: Om du vill ta bort det offentliga IP-adressprefixet, markera **ta bort** i den **översikt** avsnittet. Om adresser inom prefixet som är kopplade till offentliga IP-adressresurser, måste du först radera de offentliga IP-adressresurser. Se [ta bort en offentlig IP-adress](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ nätverket offentliga ip-Prefixlistan](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-list) till listan över offentliga IP-adresser, [az nätverket offentliga ip-prefix show](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-show) att visa inställningar. [az nätverket offentliga ip-prefix update](/cli/azure/network/public-ip-prefix#az-network-public-ip-prefix-update) att uppdatera; [az nätverket offentliga ip-prefix delete](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-delete) att ta bort|
|PowerShell|[Get-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/get-azurermpublicipaddressprefix) att hämta objekt för en offentlig IP-adress och visa dess inställningar [Set-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/set-azurermpublicipaddressprefix) att uppdatera inställningar. [Remove-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/remove-azurermpublicipaddressprefix) att ta bort|

## <a name="permissions"></a>Behörigheter

Om du vill genomföra åtgärder på offentliga IP-adressprefix, måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som visas i följande tabell:

| Åtgärd                                                                   | Namn                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Läsa en offentlig IP-adressprefix                                |
| Microsoft.Network/publicIPPrefixes/write                          | Skapa eller uppdatera en offentlig IP-adressprefix                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Ta bort en offentlig IP-adressprefix                              |
|Microsoft.Network/publicIPPrefixes/join/action | Skapa en offentlig IP-adress från ett prefix |

## <a name="next-steps"></a>Nästa steg

- Läs mer om scenarier och fördelar med att använda en [offentliga IP-prefix](public-ip-address-prefix.md)
