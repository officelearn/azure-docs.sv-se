---
title: Skapa, ändra eller ta bort en offentlig Azure-IP-adress | Microsoft-dokument
description: Lär dig hur du skapar, ändrar eller tar bort en offentlig IP-adress.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 38ff40b537730418fe9f0f8295884dae98a2fe0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244919"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Skapa, ändra eller ta bort en offentlig IP-adress

Lär dig mer om en offentlig IP-adress och hur du skapar, ändrar och tar bort en. En offentlig IP-adress är en resurs med egna konfigurerbara inställningar. Genom att tilldela en offentlig IP-adress till en Azure-resurs som stöder offentliga IP-adresser kan du:
- Inkommande kommunikation från Internet till resursen, till exempel Virtuella Azure-datorer (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways och andra. Du kan fortfarande kommunicera med vissa resurser, till exempel virtuella datorer, från Internet, om en virtuell dator inte har tilldelats en offentlig IP-adress, så länge den virtuella datorn ingår i en sluten belastningsutjämnad pool och belastningsutjämnaren tilldelas en offentlig IP-adress. Information om huruvida en resurs för en viss Azure-tjänst kan tilldelas en offentlig IP-adress, eller om den kan kommuniceras med via den offentliga IP-adressen för en annan Azure-resurs, läser du dokumentationen för tjänsten.
- Utgående anslutning till Internet med en förutsägbar IP-adress. En virtuell dator kan till exempel kommunicera utgående till Internet utan en offentlig IP-adress som tilldelats den, men dess adress är nätverksadress som översätts av Azure till en oförutsägbar offentlig adress, som standard. Genom att tilldela en offentlig IP-adress till en resurs kan du veta vilken IP-adress som används för den utgående anslutningen. Även om den är förutsägbar kan adressen ändras, beroende på vilken tilldelningsmetod som valts. Mer information finns i [Skapa en offentlig IP-adress](#create-a-public-ip-address). Mer information om utgående anslutningar från Azure-resurser finns i [Förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Slutför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto registrerar du dig för ett [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder https://portal.azure.comportalen öppnar och loggar du in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.

Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [Behörigheter](#permissions).

Offentliga IP-adresser har en nominell avgift. Om du vill visa prissättningen läser du [prissidan för IP-adress.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Ange *offentlig IP-adress* i rutan *Sök på Marketplace.* När **offentlig IP-adress** visas i sökresultaten markerar du den.
3. Under **Offentlig IP-adress**väljer du **Skapa**.
4. Ange eller välj värden för följande inställningar under **Skapa offentlig IP-adress**och välj sedan **Skapa:**

   |Inställning|Krävs?|Information|
   |---|---|---|
   |IP-version|Ja| Välj IPv4 eller IPv6 eller Båda. Om du väljer Båda skapas 2 offentliga IP-adresser- 1 IPv4-adress och 1 IPv6-adress. Läs mer om [IPv6 i Azure VNETs](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Ja|Alla offentliga IP-adresser som skapats före införandet av SKU:er är **grundläggande** SKU-offentliga IP-adresser. Du kan inte ändra SKU:n när den offentliga IP-adressen har skapats. En fristående virtuell dator, virtuella datorer i en tillgänglighetsuppsättning eller skaluppsättningar för virtuella datorer kan använda Basic eller Standard SKU: er. Det är inte tillåtet att blanda SKU:er mellan virtuella datorer i tillgänglighetsuppsättningar eller skalningsuppsättningar eller fristående virtuella datorer. **Grundläggande** SKU: Om du skapar en offentlig IP-adress i en region som stöder tillgänglighetszoner är inställningen **tillgänglighetszon** inställd på *Ingen* som standard. Grundläggande offentliga IPs stöder inte tillgänglighetszoner. **Standard** SKU: En offentlig standard-SKU-IP kan associeras till en virtuell dator eller en belastningsutjämnad front. Om du skapar en offentlig IP-adress i en region som stöder tillgänglighetszoner är inställningen **tillgänglighetszon** inställd på *Zonundant* som standard. Mer information om tillgänglighetszoner finns i inställningen **Tillgänglighetszon.** Standard-SKU krävs om du associerar adressen till en standardbelastningsutjämnare. Mer information om standardbelastningsutjämnare finns i [Standard SKU för Azure load balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.|
   |Namn|Ja|Namnet måste vara unikt inom den resursgrupp du väljer.|
   |TILLDELNING AV IP-adress|Ja|**Dynamisk:** Dynamiska adresser tilldelas först när en offentlig IP-adress är associerad med en Azure-resurs och resursen startas för första gången. Dynamiska adresser kan ändras om de tilldelas en resurs, till exempel en virtuell dator, och den virtuella datorn stoppas (frigörs) och sedan startas om. Adressen förblir densamma om en virtuell dator startas om eller stoppas (men inte deallocated). Dynamiska adresser frisläpps när en offentlig IP-adressresurs separeras från en resurs som den är kopplad till. **Statisk:** Statiska adresser tilldelas när en offentlig IP-adress skapas. Statiska adresser frisläpps inte förrän en offentlig IP-adressresurs har tagits bort. Om adressen inte är kopplad till en resurs kan du ändra tilldelningsmetoden när adressen har skapats. Om adressen är kopplad till en resurs kanske du inte kan ändra tilldelningsmetoden. Om du väljer *IPv6* för **IP-versionen**måste tilldelningsmetoden vara *Dynamisk* för Basic SKU.  Standard SKU-adresser är *statiska* för både IPv4 och IPv6. |
   |Tidsgränsen för inaktiv tid (minuter)|Inga|Hur många minuter för att hålla en TCP- eller HTTP-anslutning öppen utan att förlita sig på klienter för att skicka keep-alive-meddelanden. Om du väljer IPv6 för **IP-version**kan det här värdet inte ändras. |
   |DNS-namnetikett|Inga|Måste vara unikt inom den Azure-plats som du skapar namnet i (över alla prenumerationer och alla kunder). Azure registrerar automatiskt namnet och IP-adressen i dnsen så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät, till exempel *location.cloudapp.azure.com* (där platsen är den plats du väljer) till det namn du anger, för att skapa det fullständigt kvalificerade DNS-namnet. Om du väljer att skapa båda adressversionerna tilldelas samma DNS-namn till både IPv4- och IPv6-adresserna. Azures standard-DNS innehåller både IPv4 A- och IPv6 AAAA-namnposter och svarar med båda posterna när DNS-namnet slås upp. Klienten väljer vilken adress (IPv4 eller IPv6) som ska kommuniceras med. Istället för att använda DNS-namnetiketten med standardsuffixet, eller som ett tillägg till det, kan du använda Azure DNS-tjänsten för att konfigurera ett DNS-namn med ett anpassat suffix som motsvarar den offentliga IP-adressen. Mer information finns i [Use Azure DNS with an Azure public IP address](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) (Använda Azure DNS med en offentlig IP-adress för Azure).|
   |Namn (Visas bara om du väljer IP-version av **båda**)|Ja, om du väljer IP-version av **båda**|Namnet måste vara annorlunda än namnet du anger för **förnamnet** i den här listan. Om du väljer att skapa både en IPv4- och en IPv6-adress skapar portalen två separata offentliga IP-adressresurser, en med varje IP-adressversion tilldelad.|
   |IP-adresstilldelning (Visas bara om du väljer IP-version av **båda**)|Ja, om du väljer IP-version av **båda**|Samma begränsningar som IP-adresstilldelning ovan|
   |Prenumeration|Ja|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som den resurs som du ska associera den offentliga IP-adressen.|
   |Resursgrupp|Ja|Kan finnas i samma, eller annorlunda, [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som den resurs som du ska associera den offentliga IP-adressen.|
   |Location|Ja|Måste finnas på samma [plats](https://azure.microsoft.com/regions), även kallad region, som den resurs som du ska associera den offentliga IP:er till.|
   |Tillgänglighetszon| Inga | Den här inställningen visas bara om du väljer en plats som stöds. En lista över platser som stöds finns i [översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du har valt **basic** SKU väljs *Ingen* automatiskt åt dig. Om du föredrar att garantera en viss zon kan du välja en viss zon. Båda valen är inte zonundant. Om du har valt **Standard** SKU: Zonundant väljs automatiskt åt dig och gör datasökvägen motståndskraftig mot zonfel. Om du föredrar att garantera en viss zon, som inte är motståndskraftig mot zonfel, kan du välja en viss zon.

**Kommandon**

Även om portalen ger möjlighet att skapa två offentliga IP-adressresurser (en IPv4 och en IPv6), skapar följande CLI- och PowerShell-kommandon en resurs med en adress för en IP-version eller en annan. Om du vill ha två offentliga IP-adressresurser, en för varje IP-version, måste du köra kommandot två gånger och ange olika namn och IP-versioner för de offentliga IP-adressresurserna.

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visa, ändra inställningar för eller ta bort en offentlig IP-adress

1. Skriv offentlig *IP-adress*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **offentliga IP-adresser** visas i sökresultaten markerar du den.
2. Välj namnet på den offentliga IP-adress som du vill visa, ändra inställningar för eller ta bort från listan.
3. Slutför ett av följande alternativ, beroende på om du vill visa, ta bort eller ändra den offentliga IP-adressen.
   - **Visa**: Avsnittet **Översikt** visar viktiga inställningar för den offentliga IP-adressen, till exempel det nätverksgränssnitt som den är kopplad till (om adressen är kopplad till ett nätverksgränssnitt). Portalen visar inte versionen av adressen (IPv4 eller IPv6). Om du vill visa versionsinformationen använder du powershell- eller CLI-kommandot för att visa den offentliga IP-adressen. Om IP-adressversionen är IPv6 visas inte den tilldelade adressen av portalen, PowerShell eller CLI.
   - **Ta bort**: Om du vill ta bort den offentliga IP-adressen väljer du **Ta bort** i avsnittet **Översikt.** Om adressen för närvarande är associerad med en IP-konfiguration kan den inte tas bort. Om adressen för närvarande är associerad med en konfiguration väljer du **Separera för** att ta bort adressen från IP-konfigurationen.
   - **Ändra:** välj **Konfiguration**. Ändra inställningarna med hjälp av informationen i steg 4 i [Skapa en offentlig IP-adress](#create-a-public-ip-address). Om du vill ändra tilldelningen för en IPv4-adress från statisk till dynamisk måste du först ta bort den offentliga IPv4-adressen från den IP-konfiguration som den är kopplad till. Du kan sedan ändra tilldelningsmetoden till dynamisk och välja **Associera** för att associera IP-adressen till samma IP-konfiguration, en annan konfiguration eller lämna den separerad. Om du vill ta bort en offentlig IP-adress väljer du **Ta bort kopplingen**i avsnittet **Översikt** .

   >[!WARNING]
   >När du ändrar tilldelningsmetoden från statisk till dynamisk förlorar du IP-adressen som har tilldelats den offentliga IP-adressen. Azure-offentliga DNS-servrarna upprätthåller en mappning mellan statiska eller dynamiska adresser och en DNS-namnetikett (om du har definierat en sådan), kan en dynamisk IP-adress ändras när den virtuella datorn startas efter att ha stoppats (hanterat) tillstånd. Om du vill förhindra att adressen ändras tilldelar du en statisk IP-adress.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) to list public IP addresses, az network public-ip show to show settings; az network public-ip list to list public IP addresses, az network public-ip show to show settings; az network public-ip list to list public IP addresses, [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) to show settings; az network [Az-nätverket public-ip uppdatering](/cli/azure/network/public-ip#az-network-public-ip-update) för att uppdatera; [az nätverk public-ip ta bort](/cli/azure/network/public-ip#az-network-public-ip-delete) för att ta bort|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att hämta ett offentligt IP-adressobjekt och visa dess inställningar, [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) för att uppdatera inställningar; [Ta bort-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) för att ta bort|

## <a name="assign-a-public-ip-address"></a>Tilldela en offentlig IP-adress

Lär dig hur du tilldelar en offentlig IP-adress till följande resurser:

- En [Virtuell Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (när du skapar) eller till en befintlig [virtuell dator](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internet-vänd belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Anslutning från plats till plats med hjälp av en Azure VPN-gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skala uppsättning för virtuell dator i Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter på offentliga IP-adresser måste ditt konto tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas lämpliga åtgärder i följande tabell:

| Åtgärd                                                             | Namn                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Läsa en offentlig IP-adress                                          |
| Microsoft.Network/publicIPAddresses/write                          | Skapa eller uppdatera en offentlig IP-adress                           |
| Microsoft.Network/publicIPAdresser/borttagning                         | Ta bort en offentlig IP-adress                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associera en offentlig IP-adress till en resurs                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en offentlig IP-adress med [powershell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller med Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för offentliga IP-adresser
