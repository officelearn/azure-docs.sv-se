---
title: Skapa, ändra eller ta bort ett azure public IP-adressprefix
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar, ändrar eller tar bort ett offentligt IP-adressprefix.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 5f0c2d9757f3652b0f83b8c36d89c855f7a92fdd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383875"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Skapa, ändra eller ta bort ett prefix för offentlig IP-adress

Lär dig mer om ett offentligt IP-adressprefix och hur du skapar, ändrar och tar bort ett. Ett offentligt IP-adressprefix är ett sammanhängande adressintervall baserat på antalet offentliga IP-adresser som du anger. Adresserna tilldelas din prenumeration. När du skapar en offentlig IP-adressresurs kan du tilldela en statisk offentlig IP-adress från prefixet och associera adressen till virtuella datorer, belastningsutjämnare eller andra resurser för att aktivera internetanslutning. Om du inte är bekant med prefix för offentlig IP-adress läser du [översikt över prefix för offentlig IP-adress](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Slutför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto registrerar du dig för ett [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder https://portal.azure.comportalen öppnar och loggar du in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.

Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [Behörigheter](#permissions).

Prefix för offentliga IP-adresser har en debitering. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Skapa ett offentligt IP-adressprefix

1. Längst upp, till vänster i portalen, väljer **du + Skapa en resurs**.
2. Ange *offentligt IP-prefix* i rutan *Sök på Marketplace.* När **prefixet public IP-adress** visas i sökresultaten markerar du det.
3. Under **Prefix för offentlig IP-adress**väljer du **Skapa**.
4. Ange eller välj värden för följande inställningar under **Skapa prefix för offentlig IP-adress**och välj sedan **Skapa:**

   |Inställning|Krävs?|Information|
   |---|---|---|
   |Prenumeration|Ja|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som den resurs som du vill associera den offentliga IP-adressen till.|
   |Resursgrupp|Ja|Kan finnas i samma, eller annorlunda, [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som den resurs som du vill associera den offentliga IP-adressen till.|
   |Namn|Ja|Namnet måste vara unikt inom den resursgrupp du väljer.|
   |Region|Ja|Måste finnas i samma [region](https://azure.microsoft.com/regions)som de offentliga IP-adresser som du ska tilldela adresser från intervallet.|
   |Prefixstorlek|Ja| Storleken på det prefix du behöver. En /28 eller 16 IP-adresser är standard.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk public-ip prefix skapa](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Nytt-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Skapa en statisk offentlig IP-adress från ett prefix
När du har skapat ett prefix måste du skapa statiska IP-adresser från prefixet. Följ stegen nedan för att göra detta.

1. Skriv prefix för *offentlig IP-adress*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **prefix för offentliga IP-adresser** visas i sökresultaten markerar du det.
2. Välj det prefix som du vill skapa offentliga IPs från.
3. När den visas i sökresultaten markerar du den och klickar på **+Lägg till IP-adress** i avsnittet Översikt.
4. Ange eller välj värden för följande inställningar under **Skapa offentlig IP-adress**. Eftersom ett prefix är för Standard SKU, IPv4 och statisk behöver du bara ange följande information:

   |Inställning|Krävs?|Information|
   |---|---|---|
   |Namn|Ja|Namnet på den offentliga IP-adressen måste vara unikt inom den resursgrupp du väljer.|
   |Tidsgränsen för inaktiv tid (minuter)|Inga|Hur många minuter för att hålla en TCP- eller HTTP-anslutning öppen utan att förlita sig på klienter för att skicka keep-alive-meddelanden. |
   |DNS-namnetikett|Inga|Måste vara unikt inom Azure-regionen som du skapar namnet i (över alla prenumerationer och alla kunder). Azure registrerar automatiskt namnet och IP-adressen i dnsen så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät, till exempel *location.cloudapp.azure.com* (där platsen är den plats du väljer) till det namn du anger, för att skapa det fullständigt kvalificerade DNS-namnet. Mer information finns i [Använda Azure DNS med en offentlig Azure-IP-adress](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternativt kan du använda CLI- och PS-kommandona nedan med parametrarna --public-ip-prefix (CLI) och -PublicIpPrefix (PS) för att skapa en offentlig IP-adressresurs. 

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Visa eller ta bort ett prefix

1. Skriv prefix för *offentlig IP-adress*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **prefix för offentliga IP-adresser** visas i sökresultaten markerar du det.
2. Välj namnet på det offentliga IP-adressprefix som du vill visa, ändra inställningar för eller ta bort från listan.
3. Slutför något av följande alternativ, beroende på om du vill visa, ta bort eller ändra prefixet för offentlig IP-adress.
   - **Visa**: Avsnittet **Översikt** visar viktiga inställningar för det offentliga IP-adressprefixet, till exempel prefix.
   - **Ta bort**: Om du vill ta bort det offentliga IP-adressprefixet väljer du **Ta bort** i avsnittet **Översikt.** Om adresser i prefixet är kopplade till offentliga IP-adressresurser måste du först ta bort de offentliga IP-adressresurserna. Se [ta bort en offentlig IP-adress](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) to list public IP addresses, az network public-ip prefix show to show settings; az network public-ip prefix list to list public IP addresses, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) to show settings; az network public-ip prefix list to list public-ip prefix [Az-nätverket public-ip prefix uppdatering](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) för att uppdatera; [az nätverk public-ip prefix bort](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) för att ta bort|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) för att hämta ett offentligt IP-adressobjekt och visa dess inställningar, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) för att uppdatera inställningar; [Ta bort-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) för att ta bort|

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter med offentliga IP-adressprefix måste ditt konto tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas lämpliga åtgärder i följande tabell:

| Åtgärd                                                            | Namn                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Läsa ett offentligt IP-adressprefix                                |
| Microsoft.Network/publicIPPrefixes/write                          | Skapa eller uppdatera ett offentligt IP-adressprefix                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Ta bort ett offentligt IP-adressprefix                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Skapa en offentlig IP-adress från ett prefix |

## <a name="next-steps"></a>Nästa steg

- Läs mer om scenarier och fördelar med att använda ett [offentligt IP-prefix](public-ip-address-prefix.md)
