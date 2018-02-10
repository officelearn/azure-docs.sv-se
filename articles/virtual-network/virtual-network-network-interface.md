---
title: "Skapa, ändra eller ta bort en Azure nätverksgränssnittet | Microsoft Docs"
description: "Läs ett nätverksgränssnitt är och hur du skapar, ändra inställningarna för och ta bort ett."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: da29ecaaa0f694be3e96baebfd80c09069d7c4a8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Skapa, ändra eller ta bort ett nätverksgränssnitt

Lär dig mer om att skapa, ändra inställningar för och ta bort ett nätverksgränssnitt. Ett nätverksgränssnitt kan en virtuell dator i Azure att kommunicera med Internet, Azure och lokala resurser. När du skapar en virtuell dator med hjälp av Azure portal, skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Du kan i stället välja att skapa nätverksgränssnitt med anpassade inställningar och Lägg till en eller flera nätverksgränssnitt i en virtuell dator när du skapar den. Du kanske vill ändra standard gränssnitt för nätverksinställningar för en befintlig nätverksgränssnitt. Den här artikeln beskriver hur du skapar ett nätverksgränssnitt med anpassade inställningar, ändra befintliga inställningar, till exempel tilldelning av nätverket filter (nätverkssäkerhetsgrupp), undernättilldelning, DNS-serverinställningarna och IP-vidarebefordring och ta bort ett nätverksgränssnitt.

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, läsa den [hantera IP-adresser](virtual-network-network-interface-addresses.md) artikel. Om du behöver lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer, läsa den [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md) artikel.


## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför alla steg i alla avsnitt i den här artikeln:

- Granska de [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikeln innehåller information om begränsningar för nätverksgränssnitt.
- Logga in på Azure [portal](https://portal.azure.com), Azure-kommandoradsgränssnittet (CLI) eller Azure PowerShell med ett Azure-konto. Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure PowerShell-kommandon som är installerad. Om du vill få hjälp med PowerShell-kommandon med exempel på, skriver `get-help <command> -full`.
- Om Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln [installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI installerad. Om du vill få hjälp med CLI-kommandon, Skriv `az <command> --help`. Du kan använda Azure Cloud-gränssnittet i stället för att installera CLI och dess krav. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell **> _** längst upp i den [portal](https://portal.azure.com).

## <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

När du skapar en virtuell dator med hjälp av Azure portal, skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Om du hellre vill ange alla gränssnitt nätverksinställningarna kan du skapa ett nätverksgränssnitt med anpassade inställningar och koppla nätverksgränssnittet till en virtuell dator när du skapar den virtuella datorn (med PowerShell eller Azure CLI). Du kan också skapa ett nätverksgränssnitt och lägga till den i en befintlig virtuell dator (med PowerShell eller Azure CLI). Om du vill lära dig mer om att skapa en virtuell dator med en befintlig nätverksgränssnittet eller att lägga till eller ta bort nätverksgränssnitt från befintliga virtuella datorer, läsa den [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md) artikel. Innan du skapar ett nätverksgränssnitt måste du ha en befintlig [virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md) på samma plats och prenumeration som du skapar ett nätverksgränssnitt i.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på **+ Lägg till**.
4. I den **skapa nätverksgränssnittet** bladet som visas anger, eller Välj värden för följande inställningar och sedan klickar du på **skapa**:

    |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Namnet måste vara unikt inom resursgruppen som du väljer. Över tiden har du förmodligen flera nätverksgränssnitt i din Azure-prenumeration. Läs den [namngivningskonventioner](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) artikel för förslag när du skapar en namngivningskonvention för att hantera flera nätverksgränssnitt enklare. Namnet kan inte ändras när nätverksgränssnittet har skapats.|
    |Virtuellt nätverk|Ja|Välj det virtuella nätverket för nätverkskortet. Du kan bara tilldela ett virtuellt nätverk som finns i samma prenumeration och plats som nätverksgränssnittet ett nätverksgränssnitt. När ett nätverksgränssnitt har skapats kan ändra du inte det virtuella nätverket som den är tilldelad till. Den virtuella datorn som du lägger till nätverksgränssnitt för måste även finnas på samma plats och prenumeration som nätverksgränssnittet.|
    |Undernät|Ja|Välj ett undernät i det virtuella nätverket som du har valt. Du kan ändra undernätet nätverksgränssnittet tilldelas när den har skapats.|
    |Privata IP-adresstilldelning|Ja| I den här inställningen väljer du tilldelningsmetod för IPv4-adress. Välj bland följande tilldelning: **dynamiska:** när du väljer det här alternativet, tilldelar Azure automatiskt nästa tillgängliga adress från adressutrymmet till undernätet som du har valt. **Statiskt:** när du väljer det här alternativet måste du manuellt tilldelar en tillgänglig IP-adress inom adressutrymmet för det undernät som du har valt. Statiska och dynamiska adresser ändras inte tills du ändrar dem eller nätverksgränssnittet har tagits bort. Du kan ändra metoden tilldelning när nätverksgränssnittet har skapats. Azure DHCP-servern tilldelar den här adressen till nätverksgränssnittet i operativsystemet för den virtuella datorn.|
    |Nätverkssäkerhetsgrupp|Nej| Lämna inställd på **ingen**, Välj en befintlig [nätverkssäkerhetsgruppen](virtual-networks-nsg.md), eller [skapar en nätverkssäkerhetsgrupp](virtual-networks-create-nsg-arm-pportal.md). Nätverkssäkerhetsgrupper gör att du kan filtrera nätverkstrafik till och från ett nätverksgränssnitt. Du kan använda noll eller en säkerhetsgrupp för nätverk till ett nätverksgränssnitt. Noll eller en säkerhetsgrupp för nätverk kan också användas till undernätet för nätverksgränssnittet har tilldelats. När en nätverkssäkerhetsgrupp används för ett nätverksgränssnitt och undernätet nätverksgränssnittet har tilldelats, sker ibland oväntade resultat. Om du vill felsöka nätverkssäkerhetsgrupper som tillämpas på nätverksgränssnitt och undernät, läsa den [felsöka nätverkssäkerhetsgrupper](virtual-network-nsg-troubleshoot-portal.md#nsg) artikel.|
    |Prenumeration|Ja|Välj en av dina Azure [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Den virtuella datorn som du kan koppla ett nätverksgränssnitt för och det virtuella nätverket som du ansluter den till måste finnas i samma prenumeration.|
    |Private IP address (IPv6)|Nej| Om du markerar den här kryssrutan, har en IPv6-adress tilldelats nätverksgränssnitt, förutom IPv4-adress som tilldelats till nätverksgränssnittet. Finns det [IPv6](#IPv6) i den här artikeln viktig information om du använder IPv6 med nätverksgränssnitt. Du kan inte välja en tilldelningsmetod för IPv6-adress. Om du vill tilldela en IPv6-adress tilldelas med metoden dynamisk.
    |IPv6-namn (visas bara när den **privat IP-adress (IPv6)** är markerad) |Ja, om den **privat IP-adress (IPv6)** är markerad.| Det här namnet har tilldelats en sekundär IP-konfiguration för nätverksgränssnittet. Mer information om IP-konfigurationer i den [Visa inställningar för nätverksgränssnittet](#view-network-interface-settings) i den här artikeln.|
    |Resursgrupp|Ja|Välj en befintlig [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eller skapa en. Ett nätverksgränssnitt kan finnas i samma eller olika resursgrupp, än den virtuella datorn som du kopplar den till, eller det virtuella nätverket som du ansluter den till.|
    |Plats|Ja|Den virtuella datorn du bifogar ett nätverksgränssnitt för och det virtuella nätverket som du ansluter den till måste finnas i samma [plats](https://azure.microsoft.com/regions), vilket även kallas en region.|

Portalen ger inte alternativet att tilldela nätverkskortet en offentlig IP-adress när du skapar det, även om portalen skapa en offentlig IP-adress och tilldela den till ett nätverksgränssnitt när du skapar en virtuell dator med hjälp av portalen. Om du vill veta hur du lägger till en offentlig IP-adress till nätverksgränssnittet när du har skapat den, läsa den [hantera IP-adresser](virtual-network-network-interface-addresses.md) artikel. Om du vill skapa ett nätverksgränssnitt med en offentlig IP-adress måste du använda CLI eller PowerShell för att skapa nätverksgränssnittet.

>[!Note]
> Azure tilldelar en MAC-adress till nätverksgränssnittet när nätverksgränssnittet är kopplat till en virtuell dator och den virtuella datorn startas första gången. Du kan inte ange MAC-adress som Azure tilldelar nätverksgränssnittet. MAC-adressen är tilldelade till nätverksgränssnittet tills nätverksgränssnittet har tagits bort eller privat IP-adress som tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet har ändrats. Mer information om IP-adresser och IP-konfigurationer i [hantera IP-adresser](virtual-network-network-interface-addresses.md) artikel.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[Skapa AZ nätverket nic](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Visa inställningar för nätverksgränssnitt

Du kan visa och ändra de flesta inställningar för ett nätverksgränssnitt när den har skapats.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på nätverksgränssnittet som du vill visa eller ändra inställningar för.
4. Följande inställningar finns i bladet för nätverksgränssnittet som du har valt:
    - **Översikt:** innehåller information om nätverksgränssnitt, till exempel IP-adresser som tilldelats, för det virtuella nätverk/undernätet nätverksgränssnittet har tilldelats och den virtuella nätverksgränssnittet är ansluten till (om den är kopplad till en). Följande bild visar en översikt över inställningar för ett nätverksgränssnitt med namnet **mywebserver256**: ![översikt över gränssnittet](./media/virtual-network-network-interface/nic-overview.png) kan du flytta ett nätverksgränssnitt till en annan resursgrupp eller prenumerationen genom att klicka på (**ändra**) bredvid den **resursgruppen** eller **prenumerationsnamn**. Om du flyttar nätverksgränssnittet måste du flytta alla resurser som rör nätverksgränssnitt med den. Om nätverksgränssnittet är kopplad till en virtuell dator, till exempel måste du också flytta den virtuella datorn och andra virtuella-relaterade resurser. Om du vill flytta ett nätverksgränssnitt, läsa den [flytta resursen till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal) artikel. Artikeln visar en lista över förutsättningar och hur du flyttar resurser med hjälp av Azure portal, PowerShell och Azure CLI.
    - **IP-konfigurationer:** offentliga och privata IPv4 och IPv6-adresser tilldelas IP-konfigurationer i den här listan. Om en IPv6-adress har tilldelats en IP-konfiguration, visas inte adressen. Mer information om IP-konfigurationer och lägga till och ta bort IP-adresser i den [konfigurera IP-adresser för en Azure nätverksgränssnittet](virtual-network-network-interface-addresses.md) artikel. IP-vidarebefordring och undernättilldelning konfigureras i det här avsnittet. Mer information om dessa inställningar i [aktivera eller inaktivera IP-vidarebefordring](#enable-or-disable-ip-forwarding) och [ändra undernättilldelning](#change-subnet-assignment) avsnitt i den här artikeln.
    - **DNS-servrar:** du kan ange vilken DNS-server som tilldelas ett nätverksgränssnitt av Azure DHCP-servrar. Nätverksgränssnittet kan ärver inställningarna från det virtuella nätverket som nätverksgränssnittet har tilldelats, eller ha en inställning som åsidosätter inställningar för det virtuella nätverket som den är tilldelad till. Om du vill ändra det som visas kan du slutföra stegen i den [ändra DNS-servrar](#change-dns-servers) i den här artikeln.
    - **Nätverkssäkerhetsgrupp (NSG):** visar vilket NSG är kopplad till nätverksgränssnittet (eventuella). En NSG innehåller inkommande och utgående regler för att filtrera nätverkstrafik för nätverksgränssnittet. Om en NSG är kopplad till nätverksgränssnittet visas namnet på tillhörande NSG: N. Om du vill ändra det som visas kan du slutföra stegen i den [hantera nätverket säkerhetsassociationer för gruppen](virtual-network-manage-nsg-arm-portal.md#manage-associations) artikel.
    - **Egenskaper:** visar nyckeln inställningar för nätverksgränssnittet, inklusive dess MAC-adress (tomt om nätverksgränssnittet inte är kopplad till en virtuell dator) och prenumerationen den finns i.
    - **Effektiva säkerhetsregler:** säkerhetsregler listas om nätverksgränssnittet är ansluten till en aktiv virtuell dator och en NSG är kopplad till nätverksgränssnittet eller den undernät som den är tilldelad till. Mer information om vad som visas i [felsöka nätverkssäkerhetsgrupper](virtual-network-nsg-troubleshoot-portal.md#nsg) artikel. Mer information om NSG: er i [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md) artikel.
    - **Effektiva vägar:** vägar listas om nätverksgränssnittet är kopplat till en aktiv virtuell dator. Vägar är en kombination av Azure standardvägar några användardefinierade vägar (UDR) och BGP-vägar som kan finnas för undernätet nätverksgränssnittet har tilldelats. Mer information om vad som visas i [felsöka vägar](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface) artikel. Om du vill veta mer om Azure standard och udr: er kan läsa den [användardefinierade vägar](virtual-networks-udr-overview.md) artikel.
    - **Gemensamma inställningar för Azure Resource Manager:** om du vill veta mer om standardinställningarna för Azure Resource Manager kan du läsa den [aktivitetsloggen](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [taggar](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Låser](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), och [automatiseringsskriptet](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) artiklar.

**Kommandon**

Om en IPv6-adress tilldelas ett nätverksgränssnitt, returnerar det faktum att adressen är tilldelad, men det inte returnera den tilldelade adressen PowerShell-utdata. På liknande sätt CLI returnerar det faktum att adressen är tilldelad, men returnerar *null* i utdata för adress.

|Verktyget|Kommando|
|---|---|
|CLI|[listan över AZ nätverk nic](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_list) visa nätverksgränssnitt i prenumerationen; [az nätverket nic visa](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_show) att visa inställningar för ett nätverksgränssnitt|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) visa nätverksgränssnitt i prenumerationen eller visa inställningarna för ett nätverksgränssnitt|

## <a name="change-dns-servers"></a>Ändra DNS-servrar

DNS-servern är tilldelad av Azure DHCP-servern till nätverksgränssnitt i virtuella operativsystem. DNS-server som tilldelats är oavsett inställning för DNS-server för ett nätverksgränssnitt. Mer information om inställningarna för matchning av namn för ett nätverksgränssnitt finns [namnmatchning för virtuella datorer](virtual-networks-name-resolution-for-vms-and-role-instances.md). Nätverksgränssnittet kan ärva inställningar från det virtuella nätverket, eller använda en egen unik inställningar som åsidosätter inställningarna för det virtuella nätverket.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på nätverksgränssnittet som du vill visa eller ändra inställningar för.
4. I bladet för nätverksgränssnittet som du har valt klickar du på **DNS-servrar** under **inställningar**.
5. Klicka på antingen:
    - **Ärv virtuella nätverk (standard)**: Välj det här alternativet ska ärva inställningen DNS-server har definierats för det virtuella nätverket som nätverksgränssnittet har tilldelats. En anpassad DNS-server eller Azure-tillhandahållna DNS-server har definierats på nivån virtuellt nätverk. Azure-tillhandahållna DNS-servern kan matcha värdnamn för resurser som tilldelats till samma virtuella nätverk. FQDN måste användas för att lösa för resurser som tilldelats olika virtuella nätverk.
    - **Anpassad**: du kan konfigurera en egen DNS-server för att matcha namn över flera virtuella nätverk. Ange IP-adressen för den server som du vill använda som en DNS-server. DNS-serveradressen som du anger tilldelas till det här nätverksgränssnittet och åsidosätter eventuella DNS-inställningar för det virtuella nätverket som nätverksgränssnittet har tilldelats.
6. Klicka på **Spara**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic uppdatering](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivera eller inaktivera IP-vidarebefordring

IP-vidarebefordring kan den virtuella datorn ett nätverksgränssnitt som är kopplad till:
- Tar emot nätverkstrafik som inte är avsedda för en av IP-adresser som har tilldelats någon IP-konfigurationer som tilldelats till nätverksgränssnittet.
- Skicka trafik med en annan källa IP-adress än den som har tilldelats en IP-konfigurationer för ett nätverksgränssnitt.

Inställningen måste aktiveras för varje nätverksgränssnitt som är kopplad till den virtuella datorn som tar emot trafik som den virtuella datorn ska vidarebefordra. En virtuell dator kan vidarebefordra trafik om den har flera nätverksgränssnitt, ett enda nätverksgränssnitt som är kopplade till den. IP-vidarebefordring är en Azure-inställning, måste den virtuella datorn också köra ett program som kan vidarebefordra trafik, till exempel brandvägg, WAN-optimering och program för belastningsutjämning. När en virtuell dator körs nätverksprogram, är den virtuella datorn ofta kallas en virtuell nätverksenhet. Du kan visa en lista över redo att distribuera virtuella nätverksenheter i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-vidarebefordring är vanligt med användardefinierade vägar. Mer information om användardefinierade vägar finns i artikeln [Användardefinierade vägar](virtual-networks-udr-overview.md).

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på nätverksgränssnittet som du vill aktivera eller inaktivera IP-vidarebefordring för.
4. I bladet för nätverksgränssnittet som du har valt klickar du på **IP-konfigurationer** i den **inställningar** avsnitt.
5. Klicka på **aktiverad** eller **inaktiverade** (standardinställningen) ändra inställningen.
6. Klicka på **Spara**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic uppdatering](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Ändra undernättilldelning

Du kan ändra undernätet, men inte det virtuella nätverket, som har tilldelats ett nätverksgränssnitt.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på nätverksgränssnittet som du vill visa eller ändra inställningar för.
4. Klicka på **IP-konfigurationer** under **inställningar** i bladet för nätverksgränssnittet som du har valt. Om alla privata IP-adresser för alla IP-konfigurationer i listan har **(statisk)** bredvid dem, måste du ändra tilldelningsmetod för IP-adress till dynamisk genom att slutföra de steg som följer. Alla privata IP-adresser måste tilldelas med metoden dynamisk tilldelning ändra tilldelningen för undernätet för nätverksgränssnittet. Om adresserna tilldelas med metoden dynamiska, fortsätter du till steg 5. Om IPv4-adresser tilldelas med statisk tilldelningsmetod, utför följande steg om du vill ändra metoden tilldelning till dynamisk:
    - Klicka på IP-konfiguration som du vill ändra tilldelningsmetod för IPv4-adress för i listan över IP-konfigurationer.
    - I bladet som visas för IP-konfiguration klickar du på **dynamiska** för den **tilldelning** metod. Du kan inte tilldela en IPv6-adress med statisk tilldelningsmetod.
    - Klicka på **Spara**.
5. Välja det undernät som du vill ansluta nätverksgränssnitt för från den **undernät** listrutan.
6. Klicka på **Spara**. Nya dynamiska adresser tilldelas från adressintervallet undernät för det nya undernätet. Efter att tilldela nätverkskortet till ett nytt undernät kan tilldela du en statisk IPv4-adress från det nya adressintervallet i undernätet om du väljer. Om du vill veta mer om att lägga till, ändra och ta bort IP-adresser för ett nätverksgränssnitt kan läsa den [hantera IP-adresser](virtual-network-network-interface-addresses.md) artikel.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic ip-config uppdatering](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>Ta bort ett nätverksgränssnitt

Du kan ta bort ett nätverksgränssnitt så länge det inte är kopplat till en virtuell dator. Om den är kopplad till en virtuell dator, måste du först placera den virtuella datorn i tillståndet Stoppad (frigjord) sedan ta bort nätverksgränssnittet från den virtuella datorn innan du kan ta bort nätverksgränssnittet. Om du vill koppla ett nätverksgränssnitt från en virtuell dator, slutför du stegen i den [ta bort en nätverksgränssnittet från en virtuell dator](virtual-network-network-interface-vm.md#vm-remove-nic) avsnitt i den [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md) artikel. Om du tar bort en virtuell dator kopplas från alla nätverksgränssnitt som är kopplade till den, men tar inte bort nätverksgränssnitt.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. Högerklicka på nätverksgränssnittet som du vill ta bort och klicka på **ta bort**.
4. Klicka på **Ja** att bekräfta borttagningen av nätverksgränssnittet.

När du tar bort ett nätverksgränssnitt släpps alla MAC- eller IP-adresser som är kopplade till den.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ datornätverket nic ta bort](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Nästa steg
Om du vill skapa en virtuell dator med flera gränssnitt eller IP-adresser, Läs följande artiklar:

**Kommandon**

|Aktivitet|Verktyget|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enda NIC VM med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enda NIC VM med en privat IPv6-adress (bakom en belastningsutjämnare i Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
