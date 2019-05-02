---
title: Skapa, ändra eller ta bort en Azure offentlig IP-adress | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort en offentlig IP-adress.
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
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: e1e82d7f7b6b8bf9bfef56b569db2db097b914ab
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728732"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Skapa, ändra eller ta bort en offentlig IP-adress

Läs om en offentlig IP-adress och hur du skapar, ändra och ta bort ett. En offentlig IP-adress är en resurs med sina egna konfigurerbara inställningar. Tilldela en offentlig IP-adress till en Azure-resurs som har stöd för offentliga IP-adresser gör att:
- Inkommande kommunikation från Internet till resurs, till exempel Azure-datorer (VM), Azure Application Gateway, belastningsutjämnare för Azure, Azure VPN gateway och andra. Du kan fortfarande kommunicera med vissa resurser, till exempel virtuella datorer från Internet, om en virtuell dator inte har en offentlig IP-adress som tilldelats, så länge som den virtuella datorn är en del av en load balancer backend-poolen och belastningsutjämnaren har tilldelats en offentlig IP-adress. För att avgöra om en resurs för en specifik Azure-tjänst kan tilldelas en offentlig IP-adress eller om den kan förmedlas med via offentliga IP-adressen för en annan Azure-resurs, finns i dokumentationen för tjänsten.
- Utgående anslutning till Internet med en förutsägbar IP-adress. Exempelvis kan en virtuell dator kommunicera utgående till Internet utan en offentlig IP-adress tilldelas till den, men dess adress är nätverksadress översättas av Azure till en oförutsägbara offentlig adress som standard. Tilldela en offentlig IP-adress till en resurs kan du vet vilken IP-adress används för den utgående anslutningen. Om förutsägbara, kan adressen ändras beroende på vilken tilldelningsmetod som valts. Mer information finns i [skapa en offentlig IP-adress](#create-a-public-ip-address). Läs mer om utgående anslutningar från Azure-resurser i [förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

Offentliga IP-adresser har en nominell avgift. Om du vill se priserna, läsa den [prissättning för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan.

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. I det övre, vänstra hörnet i portalen, Välj **+ skapa en resurs**.
2. Ange *offentliga ip-adressen* i den *Sök på Marketplace* box. När **offentliga IP-adressen** visas i sökresultatet väljer du det.
3. Under **offentliga IP-adressen**väljer **skapa**.
4. Ange eller Välj värden för följande inställningar under **skapa offentlig IP-adress**och välj sedan **skapa**:

   |Inställning|Krävs?|Information|
   |---|---|---|
   |Namn|Ja|Namnet måste vara unikt inom den resursgrupp som du väljer.|
   |SKU|Ja|Alla offentliga IP-adresser som skapades före införandet av SKU: er är **grundläggande** SKU offentliga IP-adresser. Du kan inte ändra SKU: N när den offentliga IP-adressen har skapats. En fristående virtuell dator, virtuella datorer i en tillgänglighetsuppsättning eller VM-skalningsuppsättningar kan använda Basic eller Standard-SKU: er. Är inte tillåtet att blanda SKU: er mellan virtuella datorer i tillgänglighetsuppsättningar eller skalningsuppsättningar. **Basic** SKU: Om du skapar en offentlig IP-adress i en region som har stöd för tillgänglighetszoner, den **tillgänglighetszon** är inställt på *ingen* som standard. Du kan välja att välja en tillgänglighetszon för att garantera en viss zon för din offentliga IP-adress. **Standard** SKU: En Standard-SKU offentliga IP-adress kan kopplas till en virtuell dator eller en klientsidan belastningsutjämnare. Om du skapar en offentlig IP-adress i en region som har stöd för tillgänglighetszoner, den **tillgänglighetszon** är inställt på *redundantzonen* som standard. Mer information om tillgänglighetszoner finns i den **tillgänglighetszon** inställningen. Standard-SKU krävs om du associerar adressen som en standardbelastningsutjämnare. Läs mer om belastningsutjämnare som standard i [Azure load balancer standard-SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.|
   |IP-version|Ja| Välj IPv4 eller IPv6. Medan offentliga IPv4-adresser kan tilldelas till flera Azure-resurser, kan endast en offentlig IPv6-IP-adress tilldelas till en belastningsutjämnare mot Internet. Belastningsutjämnaren kan belastningsutjämna IPv6-trafik till Azure-datorer. Läs mer om [IPv6-trafik till virtuella datorer för belastningsutjämning](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du har valt den **Standard-SKU**, du har inte möjlighet att välja *IPv6*. Du kan bara skapa en IPv4-adress när du använder den **Standard-SKU**.|
   |IP-adresstilldelning|Ja|**Dynamiska:** Dynamiska adresser tilldelas när en offentlig IP-adress är kopplad till en Azure-resurs och resursen startas för första gången. Dynamiska adresser kan ändras om de är tilldelade till en resurs, till exempel en virtuell dator, och den virtuella datorn är stoppad (frigjord), och sedan startas om. Adressen ändras inte om en virtuell dator är startas om eller stoppas (men inte har frigjorts). Dynamiska adresser släpps när en offentlig IP-adressresurs har kopplats bort från en resurs som den är kopplad till. **Statisk:** Statiska adresser tilldelas när en offentlig IP-adress har skapats. Statiska adresser släpps inte förrän en offentlig IP-adressresurs har tagits bort. Om adressen inte är kopplad till en resurs, kan du ändra tilldelningsmetoden när adressen har skapats. Om adressen är kopplad till en resurs, kan du kanske inte ändra tilldelningsmetoden. Om du väljer *IPv6* för den **IP version**, tilldelningsmetoden är *dynamisk*. Om du väljer *Standard* för **SKU**, tilldelningsmetoden är *statiska*.|
   |Tidsgräns för inaktivitet (minuter)|Nej|Hur många minuter att hålla en TCP eller HTTP-anslutning öppen utan att behöva klienter skickar keep-alive-meddelanden. Om du väljer IPv6 för **IP Version**, det här värdet kan inte ändras. |
   |DNS-namnetikett|Nej|Måste vara unikt inom Azure-platsen du skapa namnet i (över alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i dess DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät som *location.cloudapp.azure.com* (där platsen är platsen du väljer) till namnet du anger, för att skapa det fullständigt kvalificerade DNS-namnet. Om du väljer att skapa båda versionerna adress tilldelas samma DNS-namn till både IPv4 och IPv6-adresser. Azures standard DNS innehåller poster för både IPv4-A- och IPv6-AAAA namn och svarar med båda posterna när DNS-namnet slås upp. Klienten väljer vilken adress (IPv4 eller IPv6) ska kunna kommunicera med. Istället för att använda DNS-namnetiketten med standardsuffixet, eller som ett tillägg till det, kan du använda Azure DNS-tjänsten för att konfigurera ett DNS-namn med ett anpassat suffix som motsvarar den offentliga IP-adressen. Mer information finns i [Use Azure DNS with an Azure public IP address](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) (Använda Azure DNS med en offentlig IP-adress för Azure).|
   |Skapa en IPv6 (eller IPv4) adress|Nej| Om IPv6- eller IPv4 visas beror på vad du väljer för **IP Version**. Exempel: Om du väljer **IPv4** för **IP Version**, **IPv6** visas här. Om du väljer *Standard* för **SKU**, du behöver inte alternativet för att skapa en IPv6-adress.
   |Namn (endast synliga när du har markerat den **skapar en IPv6 (eller IPv4) adress** kryssrutan)|Ja, om du väljer den **skapa en IPv6** (eller IPv4) kryssrutan.|Namnet måste vara annorlunda än det namn som du anger för först **namn** i den här listan. Om du väljer att skapa både en IPv4 och IPv6-adress skapar två separata offentliga IP-adressresurser, en med varje IP-adressversion som tilldelats i portalen.|
   |IP-adresstilldelning (endast synliga när du har markerat den **skapar en IPv6 (eller IPv4) adress** kryssrutan)|Ja, om du väljer den **skapa en IPv6** (eller IPv4) kryssrutan.|Om kryssrutan är **skapa en IPv4-adress**, kan du välja en tilldelningsmetod. Om kryssrutan är **skapa en IPv6-adress**, du kan inte välja en tilldelningsmetod eftersom det måste vara **dynamisk**.|
   |Prenumeration|Ja|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som du vill associera den offentliga IP-adressen till resursen.|
   |Resursgrupp|Ja|Kan finnas i samma eller olika, [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som du vill associera den offentliga IP-adressen till resursen.|
   |Location|Ja|Måste finnas i samma [plats](https://azure.microsoft.com/regions), även avses som region, som den resurs som du vill associera den offentliga IP adress till.|
   |Tillgänglighetszon| Nej | Den här inställningen visas bara om du väljer en plats som stöds. En lista över platser som stöds finns i [översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du har valt den **grundläggande** SKU, *ingen* väljs automatiskt åt dig. Om du vill garantera en viss zon kan du välja en viss zon. Alternativen är inte zonredundant. Om du har valt den **Standard** SKU: Zonredundant väljs automatiskt åt dig och gör din datasökväg elastiska för zonen fel. Om du föredrar att garantera en viss zon som inte är flexibla för zonen fel, kan du välja en viss zon.

**Kommandon**

Även om portalen ger dig möjlighet att skapa två offentliga IP-adressresurser (en IPv4 och en IPv6), skapa en resurs med en adress för en IP-version eller den andra med hjälp av följande kommandon för CLI och PowerShell. Om du vill att två offentliga IP-adressresurser, en för varje IP-version måste du köra kommandot två gånger, att ange olika namn och versioner för de offentliga IP-adressresurser.

|Verktyg|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visa, ändra inställningar för eller ta bort en offentlig IP-adress

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *offentliga ip-adressen*. När **offentliga IP-adresser** visas i sökresultaten, markerar du den.
2. Välj namnet på den offentliga IP-adressen du vill visa, ändra inställningar för eller ta bort från listan.
3. Gör något av följande alternativ, beroende på om du vill visa, ta bort eller ändra den offentliga IP-adressen.
   - **Visa**: Den **översikt** visar Nyckelinställningar för den offentliga IP-adressen, till exempel nätverksgränssnittet som den är kopplad till (om adressen är kopplad till ett nätverksgränssnitt). Versionen av adress (IPv4 eller IPv6) visas inte i portalen. Om du vill visa versionsinformationen kommandot PowerShell eller CLI för att visa den offentliga IP-adressen. Om IP-adressversion är IPv6, visas inte den tilldelade adressen som portalen, PowerShell eller CLI.
   - **Ta bort**: Om du vill ta bort den offentliga IP-adressen, Välj **ta bort** i den **översikt** avsnittet. Om adressen är för närvarande är kopplad till en IP-konfiguration, kan inte tas bort. Om adressen är associerade med en konfiguration, väljer **koppla bort** att koppla bort adress från IP-konfigurationen.
   - **Ändra**: Välj **Configuration**. Ändra inställningar med hjälp av informationen i steg 4 i [skapa en offentlig IP-adress](#create-a-public-ip-address). Om du vill ändra tilldelningen för en IPv4-adress från statisk till dynamisk, måste du först koppla bort offentlig IPv4-adress från IP-konfiguration som den är kopplad till. Du kan sedan ändra tilldelningsmetoden till dynamiska och väljer **associera** för att associera IP-adress till samma IP-konfiguration, en annan konfiguration eller om du lämna den kopplats bort. Att koppla bort en offentlig IP-adress i den **översikt** väljer **koppla bort**.

   >[!WARNING]
   >När du ändrar tilldelningsmetoden från statisk till dynamisk förlorar du IP-adressen som tilldelades till den offentliga IP-adressen. Medan Azure offentliga DNS-servrar underhålla en mappning mellan statiska eller dynamiska adresser och eventuella DNS-namnsetikett (om du har definierat en) kan ändras en dynamisk IP-adressen när den virtuella datorn startas efter att ha varit i tillståndet Stoppad (frigjord). Om du vill förhindra att adressen ändras genom att tilldela en statisk IP-adress.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network public-ip-listan](/cli/azure/network/public-ip#az-network-public-ip-list) till listan över offentliga IP-adresser, [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) att visa inställningar. [az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) att uppdatera; [az nätverket offentliga ip-ta bort](/cli/azure/network/public-ip#az-network-public-ip-delete) att ta bort|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) att hämta objekt för en offentlig IP-adress och visa dess inställningar [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) att uppdatera inställningar. [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) att ta bort|

## <a name="assign-a-public-ip-address"></a>Tilldela en offentlig IP-adress

Lär dig mer om att tilldela en offentlig IP-adress i följande resurser:

- En [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM (när du skapar) eller till en [befintlig virtuell dator](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internetuppkopplad belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Plats-till-plats-anslutning med Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

Om du vill genomföra åtgärder på offentliga IP-adresser, måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som visas i följande tabell:

| Åtgärd                                                             | Namn                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Läsa en offentlig IP-adress                                          |
| Microsoft.Network/publicIPAddresses/write                          | Skapa eller uppdatera en offentlig IP-adress                           |
| Microsoft.Network/publicIPAddresses/delete                         | Ta bort en offentlig IP-adress                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associera en offentlig IP-adress till en resurs                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en offentlig IP-adress med hjälp av [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för offentliga IP-adresser