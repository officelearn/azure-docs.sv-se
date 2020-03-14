---
title: Skapa, ändra eller ta bort en offentlig Azure-IP-adress | Microsoft Docs
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244919"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Skapa, ändra eller ta bort en offentlig IP-adress

Lär dig mer om en offentlig IP-adress och hur du skapar, ändrar och tar bort en. En offentlig IP-adress är en resurs med egna konfigurerbara inställningar. Genom att tilldela en offentlig IP-adress till en Azure-resurs som stöder offentliga IP-adresser kan du:
- Inkommande kommunikation från Internet till resursen, till exempel Azure Virtual Machines (VM), Azure Application gatewayer, Azure Load Balancer, Azure VPN-gatewayer och andra. Du kan fortfarande kommunicera med vissa resurser, t. ex. virtuella datorer, från Internet, om en virtuell dator inte har tilldelats en offentlig IP-adress, så länge den virtuella datorn är en del av en backend-pool för belastningsutjämnare, och belastningsutjämnaren tilldelas en offentlig IP-adress. Information om hur du avgör om en resurs för en särskild Azure-tjänst kan tilldelas en offentlig IP-adress, eller om den kan kommunicera med via den offentliga IP-adressen för en annan Azure-resurs, finns i dokumentationen för tjänsten.
- Utgående anslutning till Internet med en förutsägbar IP-adress. En virtuell dator kan till exempel kommunicera utgående till Internet utan att en offentlig IP-adress har tilldelats till den, men dess adress är en nätverks adress som översätts av Azure till en oförutsägbar offentlig adress som standard. Genom att tilldela en offentlig IP-adress till en resurs kan du se vilken IP-adress som används för utgående anslutning. Även om det går att förutse kan adressen ändras, beroende på vilken tilldelnings metod som valts. Mer information finns i [skapa en offentlig IP-adress](#create-a-public-ip-address). Mer information om utgående anslutningar från Azure-resurser finns i [förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.comoch loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

Offentliga IP-adresser har en nominell avgift. Om du vill visa prissättningen läser du pris sidan för [IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**.
2. Ange *offentlig IP-adress* i rutan *Sök i Marketplace* . När den **offentliga IP-adressen** visas i Sök resultaten väljer du den.
3. Under **offentlig IP-adress**väljer du **skapa**.
4. Ange eller välj värden för följande inställningar under **skapa offentlig IP-adress**och välj sedan **skapa**:

   |Inställning|Krävs?|Detaljer|
   |---|---|---|
   |IP-version|Ja| Välj IPv4 eller IPv6 eller båda. Om du väljer båda kommer två offentliga IP-adresser att skapas-1 IPv4-adress och 1 IPv6-adress. Läs mer om [IPv6 i Azure virtuella nätverk](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Ja|Alla offentliga IP-adresser som skapats innan introduktionen av SKU: er är **grundläggande** offentliga IP-adresser för SKU. Du kan inte ändra SKU när den offentliga IP-adressen har skapats. En fristående virtuell dator, virtuella datorer i en tillgänglighets uppsättning eller skalnings uppsättningar för virtuella datorer kan använda Basic-eller standard-SKU: er. Det är inte tillåtet att blanda SKU: er mellan virtuella datorer i tillgänglighets uppsättningar eller skalnings uppsättningar eller fristående virtuella datorer. **Basic** SKU: om du skapar en offentlig IP-adress i en region som har stöd för tillgänglighets zoner anges inställningen *ingen* som standard för **tillgänglighets zon** . Grundläggande offentliga IP-adresser har inte stöd för tillgänglighets zoner. **Standard** SKU: en offentlig IP-adress för standard-SKU kan associeras med en virtuell dator eller till en klient del för belastningsutjämnare. Om du skapar en offentlig IP-adress i en region som har stöd för tillgänglighets zoner anges **tillgänglighets zon** inställningen till *zon-redundant* som standard. Mer information om tillgänglighets zoner finns i inställningen **tillgänglighets zon** . Standard-SKU: n krävs om du associerar adressen med en standard belastningsutjämnare. Mer information om standardload-belastningsutjämnare finns i [Azure Load Balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.|
   |Namn|Ja|Namnet måste vara unikt inom den resurs grupp du väljer.|
   |Tilldelning av IP-adress|Ja|**Dynamisk:** Dynamiska adresser tilldelas bara efter att en offentlig IP-adress är kopplad till en Azure-resurs och resursen startas för första gången. Dynamiska adresser kan ändras om de är tilldelade till en resurs, till exempel en virtuell dator, och den virtuella datorn är stoppad (frigörs) och sedan startas om. Adressen är densamma om en virtuell dator startas om eller stoppas (men inte frigörs). Dynamiska adresser frigörs när en offentlig IP-adressresurs kopplas bort från en resurs som den är kopplad till. **Statisk:** Statiska adresser tilldelas när en offentlig IP-adress skapas. Statiska adresser frigörs inte förrän en offentlig IP-adressresurs har tagits bort. Om adressen inte är kopplad till en resurs kan du ändra tilldelnings metoden när adressen har skapats. Om adressen är kopplad till en resurs kanske du inte kan ändra tilldelnings metoden. Om du väljer *IPv6* för **IP-versionen**måste tilldelnings metoden vara *dynamisk* för Basic SKU.  Standard-SKU-adresser är *statiska* för både IPv4 och IPv6. |
   |Tids gräns för inaktivitet (minuter)|Nej|Hur många minuter som en TCP-eller HTTP-anslutning är öppen utan att lita på klienter för att skicka Keep-Alive-meddelanden. Det går inte att ändra det här värdet om du väljer IPv6 för **IP-version**. |
   |DNS-namn etikett|Nej|Måste vara unikt inom Azure-platsen som du skapar namnet i (för alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standard under nät som *location.cloudapp.Azure.com* (där platsen är den plats du väljer) till det namn som du anger för att skapa det fullständigt kvalificerade DNS-namnet. Om du väljer att skapa båda adress versionerna tilldelas samma DNS-namn till både IPv4-och IPv6-adresser. Azures standard-DNS innehåller både IPv4 A-och IPv6 AAAA-namn poster och svarar med båda posterna när DNS-namnet har sökts upp. Klienten väljer vilken adress (IPv4 eller IPv6) som ska kommunicera med. Istället för att använda DNS-namnetiketten med standardsuffixet, eller som ett tillägg till det, kan du använda Azure DNS-tjänsten för att konfigurera ett DNS-namn med ett anpassat suffix som motsvarar den offentliga IP-adressen. Mer information finns i [Use Azure DNS with an Azure public IP address](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) (Använda Azure DNS med en offentlig IP-adress för Azure).|
   |Namn (visas bara om du väljer IP-version av **båda**)|Ja, om du väljer IP-version för **båda**|Namnet får inte vara samma som det namn som du anger för **förnamnet** i den här listan. Om du väljer att skapa både en IPv4-och en IPv6-adress skapar portalen två separata offentliga IP-adressresurser, en med varje tilldelad IP-adress.|
   |Tilldelning av IP-adress (endast synlig om du väljer IP-version av **båda**)|Ja, om du väljer IP-version för **båda**|Samma begränsningar som IP-adresstilldelning ovan|
   |Prenumeration|Ja|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som den resurs som du ska koppla den offentliga IP-adressen till.|
   |Resursgrupp|Ja|Kan finnas i samma eller olika [resurs grupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som den resurs som du ska koppla den offentliga IP-adressen till.|
   |plats.|Ja|Måste finnas på samma [plats](https://azure.microsoft.com/regions), även kallat region, som den resurs som du ska koppla den offentliga IP-adressen till.|
   |Tillgänglighetszon| Nej | Den här inställningen visas bara om du väljer en plats som stöds. En lista över platser som stöds finns i [Översikt över tillgänglighets zoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du har valt den **grundläggande** SKU: n väljs *ingen* automatiskt åt dig. Om du föredrar att garantera en speciell zon kan du välja en speciell zon. Båda alternativen är inte zoner-redundanta. Om du har valt **standard** -SKU: en zon-redundant väljs automatiskt åt dig och gör din data Sök väg elastisk till zon haveri. Om du föredrar att garantera en speciell zon, som inte är elastisk till zon haveri, kan du välja en speciell zon.

**Kommandon**

Även om portalen ger möjlighet att skapa två offentliga IP-adressresurser (en IPv4 och en IPv6), skapar följande CLI-och PowerShell-kommandon en resurs med en adress för en IP-version eller en annan. Om du vill ha två offentliga IP-adressresurser, en för varje IP-version, måste du köra kommandot två gånger och ange olika namn och IP-versioner för de offentliga IP-adress resurserna.

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visa, ändra inställningar för eller ta bort en offentlig IP-adress

1. Skriv den *offentliga IP-adressen*i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **offentliga IP-adresser** visas i Sök resultaten väljer du den.
2. Välj namnet på den offentliga IP-adress som du vill visa, ändra inställningarna för eller ta bort i listan.
3. Slutför något av följande alternativ, beroende på om du vill visa, ta bort eller ändra den offentliga IP-adressen.
   - **Visa**: **översikts** avsnittet visar viktiga inställningar för den offentliga IP-adressen, till exempel det nätverks gränssnitt som den är kopplad till (om adressen är kopplad till ett nätverks gränssnitt). Portalen visar inte versionen av adressen (IPv4 eller IPv6). Om du vill visa versions informationen använder du PowerShell-eller CLI-kommandot för att visa den offentliga IP-adressen. Om IP-adress versionen är IPv6 visas inte den tilldelade adressen av portalen, PowerShell eller CLI.
   - **Ta**bort: om du vill ta bort den offentliga IP-adressen väljer du **ta bort** i **översikts** avsnittet. Om adressen för närvarande är associerad med en IP-konfiguration kan den inte tas bort. Om adressen för närvarande är associerad med en **konfiguration väljer du** koppla bort för att koppla bort adressen från IP-konfigurationen.
   - **Ändra**: Välj **konfiguration**. Ändra inställningarna med hjälp av informationen i steg 4 i [skapa en offentlig IP-adress](#create-a-public-ip-address). Om du vill ändra tilldelningen för en IPv4-adress från statisk till dynamisk måste du först koppla från den offentliga IPv4-adressen från IP-konfigurationen som den är kopplad till. Du kan sedan ändra tilldelnings metoden till dynamisk och välja **associera** för att associera IP-adressen med samma IP-konfiguration, en annan konfiguration, eller så kan du lämna den avkopplad. Om du vill koppla bort en offentlig IP-adress går du till avsnittet **Översikt** **och väljer koppla**bort.

   >[!WARNING]
   >När du ändrar tilldelnings metoden från statisk till dynamisk förlorar du IP-adressen som har tilldelats till den offentliga IP-adressen. Medan de offentliga Azure-DNS-servrarna underhåller en mappning mellan statiska eller dynamiska adresser och alla DNS-namn (om du har definierat en), kan en dynamisk IP-adress ändras när den virtuella datorn startas efter att den stoppats (frigjort). Om du vill förhindra att adressen ändras tilldelar du en statisk IP-adress.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network Public-IP List](/cli/azure/network/public-ip#az-network-public-ip-list) för att lista offentliga IP-adresser, [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show) för att Visa inställningar; [AZ Network offentlig-IP-uppdatering](/cli/azure/network/public-ip#az-network-public-ip-update) att uppdatera. [AZ Network Public-IP Delete](/cli/azure/network/public-ip#az-network-public-ip-delete) to delete|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att hämta ett objekt för en offentlig IP-adress och visa dess inställningar, [set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) för att uppdatera inställningarna. [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) att ta bort|

## <a name="assign-a-public-ip-address"></a>Tilldela en offentlig IP-adress

Lär dig hur du tilldelar en offentlig IP-adress till följande resurser:

- En virtuell [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator (när du skapar) eller till en [befintlig virtuell dator](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internet-riktade Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Plats-till-plats-anslutning med hjälp av en Azure-VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skalnings uppsättning för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter på offentliga IP-adresser måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

| Åtgärd                                                             | Namn                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Läs en offentlig IP-adress                                          |
| Microsoft. Network/publicIPAddresses/Write                          | Skapa eller uppdatera en offentlig IP-adress                           |
| Microsoft. Network/publicIPAddresses/Delete                         | Ta bort en offentlig IP-adress                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associera en offentlig IP-adress till en resurs                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en offentlig IP-adress med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för offentliga IP-adresser
