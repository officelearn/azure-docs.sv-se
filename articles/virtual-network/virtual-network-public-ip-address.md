---
title: Skapa, ändra eller ta bort en Azure offentliga IP-adress | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort en offentlig IP-adress.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: c36a3451dabbb0d08e5e475e0eec14f861bd41ce
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Skapa, ändra eller ta bort en offentlig IP-adress

Lär dig mer om en offentlig IP-adress och hur du skapar, ändra och ta bort ett. En offentlig IP-adress är en resurs med en egen konfigurerbara inställningar. Tilldela en offentlig IP-adress till andra Azure-resurser kan:
- Inkommande Internet-anslutning till resurser som virtuella datorer i Azure, Azure Virtual Machine-Skalningsuppsättningar, Azure VPN-Gateway, programgatewayer och Internet-riktade Azure belastningsutjämnare. Azure-resurser kan inte ta emot inkommande kommunikation från Internet utan en tilldelad offentliga IP-adress. Medan vissa Azure-resurser är allmänt tillgängligt via offentliga IP-adresser, måste andra resurser ha offentliga IP-adresser som tilldelats är tillgänglig från Internet.
- Utgående anslutning till Internet via en förutsägbar IP-adress. En virtuell dator kan till exempel kommunicera utgående till Internet utan en offentlig IP-adress tilldelas, men dess adress är nätverksadress översättas av Azure till ett oförutsägbart offentlig adress. Tilldela en offentlig IP-adress till en resurs kan du vet vilken IP-adress används för utgående anslutning. Även om förutsägbar, kan adressen ändras beroende på tilldelningsmetod valt. Mer information finns i [skapa en offentlig IP-adress](#create-a-public-ip-address). Mer information om utgående anslutningar från Azure-resurser i [förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel.

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.2.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.26 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

Offentliga IP-adresser har en nominell kostnad. Om du vill visa prissättning, läsa den [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. 

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *offentliga ip-adressen*. När **offentliga IP-adresser** visas i sökresultaten klickar du på den.
2. Klicka på **+ Lägg till** i den **offentliga IP-adressen** bladet som visas.
3. Ange eller Välj värden för följande inställningar i den **skapa offentlig IP-adress** bladet som visas, klicka sedan på **skapa**:

    |Inställning|Krävs?|Information|
    |---|---|---|
    |SKU|Ja|Alla offentliga IP-adresser som skapats innan introduktionen av SKU: er är **grundläggande** SKU offentliga IP-adresser.  Du kan inte ändra SKU: N efter att den offentliga IP-adressen har skapats. En fristående virtuell dator, virtuella datorer i en tillgänglighetsuppsättning eller skalningsuppsättningar i virtuella datorer kan använda Basic eller Standard SKU: er.  Blanda SKU: er mellan virtuella datorer i tillgänglighetsuppsättningar eller skaluppsättningar tillåts inte. **Grundläggande** SKU: Om du skapar en offentlig IP-adress i en region som stöder tillgänglighet zoner i **tillgänglighet zonen** inställningen *ingen* som standard. Du kan välja att välja en tillgänglighet zon att garantera en viss zon för din offentliga IP-adress. **Standard** SKU: A Standard SKU offentliga IP-Adressen kan vara kopplad till en virtuell dator eller en belastningen belastningsutjämnaren klientdel. Om du skapar en offentlig IP-adress i en region som stöder tillgänglighet zoner i **tillgänglighet zonen** inställningen *redundantzonen* som standard. Mer information om tillgänglighet zoner finns på **tillgänglighet zonen** inställningen. Standard-SKU krävs om du kopplar adressen till en belastningsutjämnare som Standard. Mer information om standard belastningsutjämnare finns [Azure belastningsutjämnare standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.|
    |Namn|Ja|Namnet måste vara unikt inom resursgruppen som du väljer.|
    |IP Version|Ja| Välj IPv4 eller IPv6. Medan offentliga IPv4-adresser kan tilldelas till flera Azure-resurser, kan en IPv6-offentliga IP-adress endast tilldelas en Internetriktade belastningsutjämnare. Belastningsutjämnaren kan belastningsutjämna IPv6-trafik till virtuella Azure-datorer. Lär dig mer om [IPv6-trafik till virtuella datorer för belastningsutjämning](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du har valt den **Standard SKU**, behöver du inte välja *IPv6*. Du kan bara skapa en IPv4-adress när du använder den **Standard SKU**.|
    |IP-adresstilldelning|Ja|**Dynamiska:** dynamiska adresser tilldelas endast när den offentliga IP-adressen är kopplad till ett nätverksgränssnitt som är kopplade till en virtuell dator och den virtuella datorn startas för första gången. Dynamiska adresser kan ändras om den virtuella datorn som nätverksgränssnittet är kopplad till har stoppats (frigjorts). Adressen förblir detsamma om den virtuella datorn har startats om eller stoppats (men inte frigöra). **Statiskt:** statiska adresser tilldelas när den offentliga IP-adressen har skapats. Statiska adresser ändras inte även om den virtuella datorn försätts i tillståndet Stoppad (frigjord). Adressen släpps endast när nätverksgränssnittet tas bort. Du kan ändra metoden tilldelning när nätverksgränssnittet har skapats. Om du väljer *IPv6* för den **IP version**, tilldelning av-metoden är *dynamiska*. Om du väljer *Standard* för **SKU**, tilldelning av-metoden är *statiska*.|
    |Tidsgränsen för inaktivitet (minuter)|Nej|Hur många minuter att hålla en TCP eller HTTP-anslutning öppen utan klienter skickar keep-alive-meddelanden. Om du väljer IPv6 för **IP Version**, det här värdet kan inte ändras. |
    |DNS-namnetikett|Nej|Måste vara unika inom den Azure-plats som du skapar namnet i (över alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i dess DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät som *location.cloudapp.azure.com* (där platsen är platsen du väljer) med namnet du anger, för att skapa det fullständigt kvalificerade DNS-namnet. Om du väljer att skapa båda versionerna av adress tilldelas samma DNS-namn till både IPv4 och IPv6-adresser. Azures standard DNS innehåller poster för både IPv4-A- och IPv6-AAAA namn och svarar med båda posterna när DNS-namnet slås upp. Klienten väljer vilken adress (IPv4 eller IPv6) ska kunna kommunicera med. Istället för att använda DNS-namnetiketten med standardsuffixet, eller som ett tillägg till det, kan du använda Azure DNS-tjänsten för att konfigurera ett DNS-namn med ett anpassat suffix som motsvarar den offentliga IP-adressen. Mer information finns i [Use Azure DNS with an Azure public IP address](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) (Använda Azure DNS med en offentlig IP-adress för Azure).|
    |Skapa en IPv6 (eller IPv4) adress|Nej| Om IPv6 och IPv4 visas beror på vad du väljer för **IP Version**. Om du väljer exempelvis **IPv4** för **IP Version**, **IPv6** visas här. Om du väljer *Standard* för **SKU**, inte har du möjlighet att skapa en IPv6-adress.
    |Namnet (endast synliga när du har markerat den **skapar en IPv6 (eller IPv4) adress** kryssrutan)|Ja, om du väljer den **skapa en IPv6** (eller IPv4) kryssrutan.|Namnet måste vara ett annat än det namn du anger för först **namn** i den här listan. Om du väljer att skapa både en IPv4 och IPv6-adress, skapar portalen två separata offentliga IP-adress resurserna, en för varje IP-adress-version som tilldelats.|
    |IP-adresstilldelning (endast synliga när du har markerat den **skapar en IPv6 (eller IPv4) adress** kryssrutan)|Ja, om du väljer den **skapa en IPv6** (eller IPv4) kryssrutan.|Om kryssrutan säger **skapar en IPv4-adress**, kan du välja en tilldelningsmetod. Om kryssrutan säger **skapar en IPv6-adress**, du kan inte välja en tilldelningsmetod eftersom det måste vara **dynamiska**.|
    |Prenumeration|Ja|Det måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som resursen som du vill associera offentliga IP-adressen.|
    |Resursgrupp|Ja|Det kan finnas i samma eller olika, [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som resursen som du vill associera offentliga IP-adressen.|
    |Plats|Ja|Det måste finnas i samma [plats](https://azure.microsoft.com/regions), även avses som region som resursen som du vill associera offentlig IP adress till.|
    |Tillgänglighetszon| Nej | Den här inställningen visas bara om du väljer en plats som stöds. En lista över platser som stöds, se [översikt över tillgänglighet zoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tillgänglighet zoner är för närvarande i förhandsversionen. Innan du väljer en zon eller zonredundant alternativet, måste du slutföra stegen i [registrera sig för tillgänglighet zoner förhandsversion](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#get-started-with-the-availability-zones-preview). Om du har valt den **grundläggande** SKU, *ingen* väljs automatiskt för dig. Om du föredrar att garantera en viss zon kan du välja en viss zon. Alternativen är inte zonredundant. Om du har valt den **Standard** SKU: zonredundant väljs automatiskt och gör din datasökväg flexibel för zonen fel. Om du föredrar att garantera en viss zon som inte är känsligt för zonen fel, kan du välja en viss zon.
  

**Kommandon**

Även om portalen ger möjlighet att skapa två offentliga IP-adressresurser (en IPv4- och en IPv6), skapa en resurs med en adress för en IP-version eller den andra följande CLI och PowerShell-kommandon. Om du vill att två offentliga IP-adressresurser, ett för varje IP-version kör du kommandot två gånger för att ange olika namn och versioner för de offentliga IP-adress-resurserna. 

|Verktyget|Kommando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visa, ändra inställningar för eller ta bort en offentlig IP-adress

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *offentliga ip-adressen*. När **offentliga IP-adresser** visas i sökresultaten klickar du på den.
2. I den **offentliga IP-adresser** bladet som visas, klicka på namnet på den offentliga IP-adressen som du vill visa, ändra inställningar för eller ta bort.
3. Gör något av följande alternativ beroende på om du vill visa, ta bort eller ändra den offentliga IP-adressen i bladet som visas för den offentliga IP-adressen.
    - **Visa**: den **översikt** på bladet visar inställningar för offentliga IP-adress, till exempel nätverksgränssnitt som den är kopplad till (om adressen är kopplad till ett nätverksgränssnitt). Portalen visar inte versionen av adress (IPv4 eller IPv6). Om du vill visa versionsinformationen kommandot PowerShell eller CLI för att visa den offentliga IP-adressen. Om IP-adressversion är IPv6, visas inte den tilldelade adressen av portalen, PowerShell eller CLI. 
    - **Ta bort**: ta bort den offentliga IP-adressen, klicka på **ta bort** i den **översikt** på bladet. Om adressen är för närvarande är kopplad till en IP-konfiguration kan inte tas bort. Om adressen är för närvarande associerad med en konfiguration, klickar du på **ta bort kopplingen** att koppla bort adress från IP-konfigurationen.
    - **Ändra**: Klicka på **Configuration**. Ändra inställningar med hjälp av informationen i steg 4 i den [skapa en offentlig IP-adress](#create-a-public-ip-address) i den här artikeln. Om du vill ändra tilldelningen för en IPv4-adress från statisk till dynamisk, måste du först koppla bort offentliga IPv4-adress från IP-konfiguration som den är kopplad till. Du kan ändra metoden tilldelning till en dynamisk och klicka på **associera** för att associera IP-adress till samma IP-konfiguration, en annan konfiguration eller lämna den kopplats bort. Koppla bort en offentlig IP-adress i den **översikt** klickar du på **ta bort kopplingen**.

>[!WARNING]
>När du ändrar metoden tilldelning från statisk till dynamisk förlorar du IP-adressen som har tilldelats till den offentliga IP-adressen. Medan Azure offentliga DNS-servrar som underhåller mappningen mellan statisk eller dynamisk adresser och eventuella DNS-namnetikett (om du har definierat ett), kan en dynamisk IP-adress ändra när den virtuella datorn startas efter att ha varit i tillståndet Stoppad (frigjord). Tilldela en statisk IP-adress för att förhindra att adressen ändras.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverk offentliga IP-adresser](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_list) lista offentliga IP-adresser, [az nätverk offentlig ip visa](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_show) att visa inställningar. [az nätverket offentliga ip-uppdatering](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_update) att uppdatera; [az nätverket offentliga IP-ta bort](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_delete) att ta bort|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) att hämta en offentlig IP-adress-objekt och visa dess inställningar [Set AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) att uppdatera inställningarna för; [Ta bort AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) att ta bort|

## <a name="next-steps"></a>Nästa steg
Tilldela offentliga IP-adresser när du skapar följande Azure-resurser:

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorer
- [Internet-riktade Azure belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Plats-till-plats-anslutning med en Azure VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skaluppsättning för virtuell Azure-dator](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
