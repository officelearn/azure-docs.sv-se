---
title: Skapa, ändra eller ta bort en Azure nätverksgränssnittet | Microsoft Docs
description: Läs ett nätverksgränssnitt är och hur du skapar, ändra inställningarna för och ta bort ett.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: e86353703d4eb8ee9acc251d62cf77d139d18ddb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Skapa, ändra eller ta bort ett nätverksgränssnitt

Lär dig mer om att skapa, ändra inställningar för och ta bort ett nätverksgränssnitt. Ett nätverksgränssnitt kan en virtuell dator i Azure att kommunicera med internet, Azure och lokala resurser. När du skapar en virtuell dator med hjälp av Azure portal, skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Du kan i stället välja att skapa nätverksgränssnitt med anpassade inställningar och Lägg till en eller flera nätverksgränssnitt i en virtuell dator när du skapar den. Du kanske vill ändra standard gränssnitt för nätverksinställningar för en befintlig nätverksgränssnitt. Den här artikeln beskriver hur du skapar ett nätverksgränssnitt med anpassade inställningar, ändra befintliga inställningar, till exempel tilldelning av nätverket filter (nätverkssäkerhetsgrupp), undernättilldelning, DNS-serverinställningarna och IP-vidarebefordring och ta bort ett nätverksgränssnitt.

Om du behöver för att lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, se [hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du behöver lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer, se [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

Kontot du loggar in, eller Anslut till Azure med, måste vara tilldelade till den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

När du skapar en virtuell dator med hjälp av Azure portal, skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Om du hellre vill ange alla gränssnitt nätverksinställningarna kan du skapa ett nätverksgränssnitt med anpassade inställningar och koppla nätverksgränssnittet till en virtuell dator när du skapar den virtuella datorn (med PowerShell eller Azure CLI). Du kan också skapa ett nätverksgränssnitt och lägga till den i en befintlig virtuell dator (med PowerShell eller Azure CLI). Om du vill lära dig mer om att skapa en virtuell dator med en befintlig nätverksgränssnittet eller att lägga till eller ta bort nätverksgränssnitt från befintliga virtuella datorer, se [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md). Innan du skapar ett nätverksgränssnitt måste du ha en befintlig [virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network) på samma plats och prenumeration som du skapar ett nätverksgränssnitt i.

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj **+ Lägg till** under **nätverksgränssnitt**.
3. Anger, eller Välj värden för följande inställningar och sedan **skapa**:

    |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Namnet måste vara unikt inom resursgruppen som du väljer. Över tiden har du förmodligen flera nätverksgränssnitt i din Azure-prenumeration. Förslag när du skapar en namngivningskonvention att hantera flera nätverksgränssnitt som är enklare finns [namngivningskonventioner](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Namnet kan inte ändras när nätverksgränssnittet har skapats.|
    |Virtuellt nätverk|Ja|Välj det virtuella nätverket för nätverkskortet. Du kan bara tilldela ett virtuellt nätverk som finns i samma prenumeration och plats som nätverksgränssnittet ett nätverksgränssnitt. När ett nätverksgränssnitt har skapats kan ändra du inte det virtuella nätverket som den är tilldelad till. Den virtuella datorn som du lägger till nätverksgränssnitt för måste även finnas på samma plats och prenumeration som nätverksgränssnittet.|
    |Undernät|Ja|Välj ett undernät i det virtuella nätverket som du har valt. Du kan ändra undernätet nätverksgränssnittet tilldelas när den har skapats.|
    |Privata IP-adresstilldelning|Ja| I den här inställningen väljer du tilldelningsmetod för IPv4-adress. Välj bland följande tilldelning: **dynamiska:** när du väljer det här alternativet, tilldelar Azure automatiskt nästa tillgängliga adress från adressutrymmet till undernätet som du har valt. **Statiskt:** när du väljer det här alternativet måste du manuellt tilldelar en tillgänglig IP-adress inom adressutrymmet för det undernät som du har valt. Statiska och dynamiska adresser ändras inte tills du ändrar dem eller nätverksgränssnittet har tagits bort. Du kan ändra metoden tilldelning när nätverksgränssnittet har skapats. Azure DHCP-servern tilldelar den här adressen till nätverksgränssnittet i operativsystemet för den virtuella datorn.|
    |Nätverkssäkerhetsgrupp|Nej| Lämna inställd på **ingen**, Välj en befintlig [nätverkssäkerhetsgruppen](security-overview.md), eller [skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md). Nätverkssäkerhetsgrupper gör att du kan filtrera nätverkstrafik till och från ett nätverksgränssnitt. Du kan använda noll eller en säkerhetsgrupp för nätverk till ett nätverksgränssnitt. Noll eller en säkerhetsgrupp för nätverk kan också användas till undernätet för nätverksgränssnittet har tilldelats. När en nätverkssäkerhetsgrupp används för ett nätverksgränssnitt och undernätet nätverksgränssnittet har tilldelats, sker ibland oväntade resultat. Om du vill felsöka nätverkssäkerhetsgrupper som tillämpas på nätverksgränssnitt och undernät, se [felsöka nätverkssäkerhetsgrupper](virtual-network-nsg-troubleshoot-portal.md#nsg).|
    |Prenumeration|Ja|Välj en av dina Azure [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Den virtuella datorn som du kan koppla ett nätverksgränssnitt för och det virtuella nätverket som du ansluter den till måste finnas i samma prenumeration.|
    |Privat IP-adress (IPv6)|Nej| Om du markerar den här kryssrutan, har en IPv6-adress tilldelats nätverksgränssnitt, förutom IPv4-adress som tilldelats till nätverksgränssnittet. Finns det [IPv6](#IPv6) i den här artikeln viktig information om du använder IPv6 med nätverksgränssnitt. Du kan inte välja en tilldelningsmetod för IPv6-adress. Om du vill tilldela en IPv6-adress tilldelas med metoden dynamisk.
    |IPv6-namn (visas bara när den **privat IP-adress (IPv6)** är markerad) |Ja, om den **privat IP-adress (IPv6)** är markerad.| Det här namnet har tilldelats en sekundär IP-konfiguration för nätverksgränssnittet. Läs mer om IP-konfigurationer i [Visa inställningar för nätverksgränssnittet](#view-network-interface-settings).|
    |Resursgrupp|Ja|Välj en befintlig [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eller skapa en. Ett nätverksgränssnitt kan finnas i samma eller olika resursgrupp, än den virtuella datorn som du kopplar den till, eller det virtuella nätverket som du ansluter den till.|
    |Plats|Ja|Den virtuella datorn du bifogar ett nätverksgränssnitt för och det virtuella nätverket som du ansluter den till måste finnas i samma [plats](https://azure.microsoft.com/regions), vilket även kallas en region.|

Portalen ger inte alternativet att tilldela nätverkskortet en offentlig IP-adress när du skapar det, även om portalen skapa en offentlig IP-adress och tilldela den till ett nätverksgränssnitt när du skapar en virtuell dator med hjälp av portalen. Information om hur du lägger till en offentlig IP-adress till nätverksgränssnittet efter att det finns [hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du vill skapa ett nätverksgränssnitt med en offentlig IP-adress måste du använda CLI eller PowerShell för att skapa nätverksgränssnittet.

Portalen ger inte alternativet att tilldela nätverksgränssnittet till säkerhetsgrupper för programmet, men Azure CLI och PowerShell. Läs mer om programmet säkerhetsgrupper i [programmet säkerhetsgrupper](security-overview.md#application-security-groups).

>[!Note]
> Azure tilldelar en MAC-adress till nätverksgränssnittet när nätverksgränssnittet är kopplat till en virtuell dator och den virtuella datorn startas första gången. Du kan inte ange MAC-adress som Azure tilldelar nätverksgränssnittet. MAC-adressen är tilldelade till nätverksgränssnittet tills nätverksgränssnittet har tagits bort eller privat IP-adress som tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet har ändrats. Läs mer om IP-adresser och IP-konfigurationer i [hantera IP-adresser](virtual-network-network-interface-addresses.md)

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface#create)|

## <a name="view-network-interface-settings"></a>Visa inställningar för nätverksgränssnitt

Du kan visa och ändra de flesta inställningar för ett nätverksgränssnitt när den har skapats. Portalen visar inte DNS-suffix eller program medlemskap i säkerhetsgruppen för nätverksgränssnittet. Du kan använda PowerShell eller Azure CLI [kommandon](#view-settings-commands) att visa DNS-suffix och programmet medlemskap i säkerhetsgruppen.

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnitt som du vill visa eller ändra inställningar för i listan.
3. Följande objekt visas för nätverksgränssnittet som du har valt:
    - **Översikt:** innehåller information om nätverksgränssnitt, till exempel IP-adresser som tilldelats, för det virtuella nätverk/undernätet nätverksgränssnittet har tilldelats och den virtuella nätverksgränssnittet är ansluten till (om den är kopplad till en). Följande bild visar en översikt över inställningar för ett nätverksgränssnitt med namnet **mywebserver256**: ![översikt över gränssnittet](./media/virtual-network-network-interface/nic-overview.png) kan du flytta ett nätverksgränssnitt till en annan resursgrupp eller prenumerationen genom att välja (**ändra**) bredvid den **resursgruppen** eller **prenumerationsnamn**. Om du flyttar nätverksgränssnittet måste du flytta alla resurser som rör nätverksgränssnitt med den. Om nätverksgränssnittet är kopplad till en virtuell dator, till exempel måste du också flytta den virtuella datorn och andra virtuella-relaterade resurser. Om du vill flytta ett nätverksgränssnitt finns [flytta resursen till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Artikeln visar en lista över förutsättningar och hur du flyttar resurser med hjälp av Azure portal, PowerShell och Azure CLI.
    - **IP-konfigurationer:** offentliga och privata IPv4 och IPv6-adresser tilldelas IP-konfigurationer i den här listan. Om en IPv6-adress har tilldelats en IP-konfiguration, visas inte adressen. Läs mer om IP-konfigurationer och lägga till och ta bort IP-adresser i [konfigurera IP-adresser för en Azure nätverksgränssnittet](virtual-network-network-interface-addresses.md). IP-vidarebefordring och undernättilldelning konfigureras i det här avsnittet. Mer information om dessa inställningar finns [aktivera eller inaktivera IP-vidarebefordring](#enable-or-disable-ip-forwarding) och [ändra undernättilldelning](#change-subnet-assignment).
    - **DNS-servrar:** du kan ange vilken DNS-server som tilldelas ett nätverksgränssnitt av Azure DHCP-servrar. Nätverksgränssnittet kan ärver inställningarna från det virtuella nätverket som nätverksgränssnittet har tilldelats, eller ha en inställning som åsidosätter inställningar för det virtuella nätverket som den är tilldelad till. Om du vill ändra vad som visas i avsnittet [ändra DNS-servrar](#change-dns-servers).
    - **Nätverkssäkerhetsgrupp (NSG):** visar vilket NSG är kopplad till nätverksgränssnittet (eventuella). En NSG innehåller inkommande och utgående regler för att filtrera nätverkstrafik för nätverksgränssnittet. Om en NSG är kopplad till nätverksgränssnittet visas namnet på tillhörande NSG: N. Om du vill ändra vad som visas i avsnittet [koppla eller koppla bort en nätverkssäkerhetsgrupp](#associate-or-dissociate-a-network-security-group).
    - **Egenskaper:** visar nyckeln inställningar för nätverksgränssnittet, inklusive dess MAC-adress (tomt om nätverksgränssnittet inte är kopplad till en virtuell dator) och prenumerationen den finns i.
    - **Effektiva säkerhetsregler:** säkerhetsregler listas om nätverksgränssnittet är ansluten till en aktiv virtuell dator och en NSG är kopplad till nätverksgränssnittet eller den undernät som den är tilldelad till. Läs mer om vad som visas i [visa effektiva säkerhetsregler](#view-effective-security-rules). Läs mer om NSG: er i [Nätverkssäkerhetsgrupper](security-overview.md).
    - **Effektiva vägar:** vägar listas om nätverksgränssnittet är kopplat till en aktiv virtuell dator. Vägar är en kombination av Azure standardvägar några användardefinierade vägar och BGP-vägar som kan finnas för undernätet nätverksgränssnittet har tilldelats. Läs mer om vad som visas i [visa effektiva vägar](#view-effective-routes). Läs mer om Azure standardvägar och användardefinierade vägar i [routning: översikt](virtual-networks-udr-overview.md).
    - **Gemensamma inställningar för Azure Resource Manager:** mer information om gemensamma inställningar för Azure Resource Manager finns [aktivitetsloggen](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Låser](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), och [automatiseringsskriptet](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

<a name="view-settings-commands"></a>**Kommandon**

Om en IPv6-adress tilldelas ett nätverksgränssnitt, returnerar det faktum att adressen är tilldelad, men det inte returnera den tilldelade adressen PowerShell-utdata. På liknande sätt CLI returnerar det faktum att adressen är tilldelad, men returnerar *null* i utdata för adress.

|Verktyget|Kommando|
|---|---|
|CLI|[listan över AZ nätverk nic](/cli/azure/network/nic#az_network_nic_list) visa nätverksgränssnitt i prenumerationen; [az nätverket nic visa](/cli/azure/network/nic#az_network_nic_show) att visa inställningar för ett nätverksgränssnitt|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) visa nätverksgränssnitt i prenumerationen eller visa inställningarna för ett nätverksgränssnitt|

## <a name="change-dns-servers"></a>Ändra DNS-servrar

DNS-servern är tilldelad av Azure DHCP-servern till nätverksgränssnitt i virtuella operativsystem. DNS-server som tilldelats är oavsett inställning för DNS-server för ett nätverksgränssnitt. Mer information om inställningarna för matchning av namn för ett nätverksgränssnitt finns [namnmatchning för virtuella datorer](virtual-networks-name-resolution-for-vms-and-role-instances.md). Nätverksgränssnittet kan ärva inställningar från det virtuella nätverket, eller använda en egen unik inställningar som åsidosätter inställningarna för det virtuella nätverket.

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj det nätverksgränssnitt som du vill ändra en DNS-server i listan.
3. Välj **DNS-servrar** under **inställningar**.
4. Välj antingen:
    - **Ärv virtuellt nätverk**: Välj det här alternativet ska ärva inställningen DNS-server har definierats för det virtuella nätverket som nätverksgränssnittet har tilldelats. En anpassad DNS-server eller Azure-tillhandahållna DNS-server har definierats på nivån virtuellt nätverk. Azure-tillhandahållna DNS-servern kan matcha värdnamn för resurser som tilldelats till samma virtuella nätverk. FQDN måste användas för att lösa för resurser som tilldelats olika virtuella nätverk.
    - **Anpassad**: du kan konfigurera en egen DNS-server för att matcha namn över flera virtuella nätverk. Ange IP-adressen för den server som du vill använda som en DNS-server. DNS-serveradressen som du anger tilldelas till det här nätverksgränssnittet och åsidosätter eventuella DNS-inställningar för det virtuella nätverket som nätverksgränssnittet har tilldelats.
5. Välj **Spara**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic uppdatering](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivera eller inaktivera IP-vidarebefordring

IP-vidarebefordring kan den virtuella datorn ett nätverksgränssnitt som är kopplad till:
- Tar emot nätverkstrafik som inte är avsedda för en av IP-adresser som har tilldelats någon IP-konfigurationer som tilldelats till nätverksgränssnittet.
- Skicka trafik med en annan källa IP-adress än den som har tilldelats en IP-konfigurationer för ett nätverksgränssnitt.

Inställningen måste aktiveras för varje nätverksgränssnitt som är kopplad till den virtuella datorn som tar emot trafik som den virtuella datorn ska vidarebefordra. En virtuell dator kan vidarebefordra trafik om den har flera nätverksgränssnitt, ett enda nätverksgränssnitt som är kopplade till den. IP-vidarebefordring är en Azure-inställning, måste den virtuella datorn också köra ett program som kan vidarebefordra trafik, till exempel brandvägg, WAN-optimering och program för belastningsutjämning. När en virtuell dator körs nätverksprogram, är den virtuella datorn ofta kallas en virtuell nätverksenhet. Du kan visa en lista över redo att distribuera virtuella nätverksenheter i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-vidarebefordring är vanligt med användardefinierade vägar. Mer information om användardefinierade vägar finns [användardefinierade vägar](virtual-networks-udr-overview.md).

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj det nätverksgränssnitt som du vill aktivera eller inaktivera IP-vidarebefordring för.
3. Välj **IP-konfigurationer** i den **inställningar** avsnitt.
4. Välj **aktiverad** eller **inaktiverade** (standardinställningen) ändra inställningen.
5. Välj **Spara**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic uppdatering](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="change-subnet-assignment"></a>Ändra undernättilldelning

Du kan ändra undernätet, men inte det virtuella nätverket, som har tilldelats ett nätverksgränssnitt.

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj det nätverksgränssnitt som du vill ändra undernättilldelning för.
3. Välj **IP-konfigurationer** under **inställningar**. Om alla privata IP-adresser för alla IP-konfigurationer i listan har **(statisk)** bredvid dem, måste du ändra tilldelningsmetod för IP-adress till dynamisk genom att slutföra de steg som följer. Alla privata IP-adresser måste tilldelas med metoden dynamisk tilldelning ändra tilldelningen för undernätet för nätverksgränssnittet. Om adresserna tilldelas med metoden dynamiska, fortsätter du till steg 5. Om IPv4-adresser tilldelas med statisk tilldelningsmetod, utför följande steg om du vill ändra metoden tilldelning till dynamisk:
    - Välj IP-konfiguration som du vill ändra tilldelningsmetod för IPv4-adress för i listan över IP-konfigurationer.
    - Välj **dynamiska** för privata IP-adressen **tilldelning** metod. Du kan inte tilldela en IPv6-adress med statisk tilldelningsmetod.
    - Välj **Spara**.
4. Välja det undernät som du vill flytta nätverksgränssnitt för från den **undernät** listrutan.
5. Välj **Spara**. Nya dynamiska adresser tilldelas från adressintervallet undernät för det nya undernätet. Efter att tilldela nätverkskortet till ett nytt undernät kan tilldela du en statisk IPv4-adress från det nya adressintervallet i undernätet om du väljer. Läs mer om att lägga till, ändra och ta bort IP-adresser för ett nätverksgränssnitt i [hantera IP-adresser](virtual-network-network-interface-addresses.md).

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic ip-config uppdatering](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Lägg till eller ta bort från programmet säkerhetsgrupper

Portalen inte alternativet att tilldela ett nätverksgränssnitt till eller ta bort ett nätverksgränssnitt från programmet säkerhetsgrupper, men Azure CLI och PowerShell. Läs mer om programmet säkerhetsgrupper i [programmet säkerhetsgrupper](security-overview.md#application-security-groups) och [skapa en säkerhetsgrupp för programmet](#create-an-application-security-group).

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic uppdatering](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Koppla eller koppla bort en nätverkssäkerhetsgrupp

1. Skriv i sökrutan överst i portalen *nätverksgränssnitt* i sökrutan. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnittet i listan som du vill koppla en nätverkssäkerhetsgrupp till eller koppla bort en nätverkssäkerhetsgrupp från.
3. Välj **nätverkssäkerhetsgruppen** under **inställningar**.
4. Välj **Redigera**.
5. Välj **nätverkssäkerhetsgruppen** och välj sedan nätverkssäkerhetsgruppen som du vill koppla till nätverksgränssnittet eller välj **ingen**, för att koppla bort en nätverkssäkerhetsgrupp.
6. Välj **Spara**.

**Kommandon**

- Azure CLI: [az nätverket nic uppdatering](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)

## <a name="delete-a-network-interface"></a>Ta bort ett nätverksgränssnitt

Du kan ta bort ett nätverksgränssnitt så länge det inte är kopplat till en virtuell dator. Om en nätverksgränssnittet är kopplat till en virtuell dator, måste du först placera den virtuella datorn i tillståndet Stoppad (frigjord) och ta bort nätverksgränssnittet från den virtuella datorn. Om du vill koppla ett nätverksgränssnitt från en virtuell dator, slutför du stegen i [ta bort en nätverksgränssnittet från en virtuell dator](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Du kan inte koppla från ett nätverksgränssnitt från en virtuell dator om den är kopplad till den virtuella datorn men de nätverksgränssnittet. En virtuell dator måste alltid ha minst ett nätverksgränssnitt som är kopplade till den. Om du tar bort en virtuell dator kopplas från alla nätverksgränssnitt som är kopplade till den, men tar inte bort nätverksgränssnitt.

1. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj **...**  på höger sida av nätverksgränssnitt som du vill ta bort från listan över nätverksgränssnitt.
3. Välj **Ta bort**.
4. Välj **Ja** att bekräfta borttagningen av nätverksgränssnittet.

När du tar bort ett nätverksgränssnitt släpps alla MAC- eller IP-adresser som är kopplade till den.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ datornätverket nic ta bort](/cli/azure/network/nic#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Lösa problem med nätverksanslutningen

Om du inte kan kommunicera till eller från en virtuell dator, säkerhetsregler för nätverkssäkerhetsgrupper eller vägar som gäller för ett nätverksgränssnitt kan vara orsaken till problemet. Du har följande alternativ för att lösa problemet:

### <a name="view-effective-security-rules"></a>Visa effektiva säkerhetsregler

De effektiva säkerhetsregler för varje nätverksgränssnitt som är kopplad till en virtuell dator är en kombination av de regler som du har skapat i en säkerhetsgrupp för nätverk och [standard säkerhetsregler](security-overview.md#default-security-rules). Så här fungerar effektivt säkerhetsregler för ett nätverksgränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera till eller från en virtuell dator. Du kan visa de effektiva reglerna för alla nätverksgränssnitt som är kopplad till en aktiv virtuell dator.

1. Ange namnet på en virtuell dator som du vill visa effektiva säkerhetsregler för i sökrutan överst i portalen. Om du inte vet namnet på en virtuell dator, ange *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten markerar du den och väljer en virtuell dator i listan.
2. Välj **nätverk** under **inställningar**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **effektiva säkerhetsregler** under **stöd + felsökning**.
5. Granska listan över giltiga säkerhetsregler för att avgöra om det finns rätt regler för ditt nödvändiga inkommande och utgående kommunikation. Mer information om vad som visas i listan i [nätverk Säkerhetsöversikt för gruppen](security-overview.md).

IP-flöde Kontrollera funktion i Azure Nätverksbevakaren kan också hjälpa dig att avgöra om säkerhetsregler förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Läs mer i [IP-flöde Kontrollera](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az nätverk nic lista-gällande-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 

### <a name="view-effective-routes"></a>Visa effektiva flöden

Effektiva vägar i nätverksgränssnitt som är kopplad till en virtuell dator är en kombination av standardvägar, alla vägar som du har skapat och alla vägar sprids från lokala nätverk via BGP via en gateway för virtuella Azure-nätverket. Förstå effektiva vägar för ett nätverksgränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera till eller från en virtuell dator. Du kan visa de effektiva vägarna för alla nätverksgränssnitt som är kopplad till en aktiv virtuell dator.

1. Ange namnet på en virtuell dator som du vill visa effektiva säkerhetsregler för i sökrutan överst i portalen. Om du inte vet namnet på en virtuell dator, ange *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten markerar du den och väljer en virtuell dator i listan.
2. Välj **nätverk** under **inställningar**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **effektiva vägar** under **stöd + felsökning**.
5. Granska listan över effektiva vägar för att avgöra om det finns rätt vägar för ditt nödvändiga inkommande och utgående kommunikation. Mer information om vad som visas i listan i [routning: översikt](virtual-networks-udr-overview.md).

Nästa hopp-funktion i Azure Nätverksbevakaren kan också hjälpa dig att avgöra om vägar förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Läs mer i [nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az nätverk nic visa-gällande--routningstabellen](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)

## <a name="permissions"></a>Behörigheter

Om du vill utföra aktiviteter på nätverksgränssnitt måste ditt konto måste ha tilldelats den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats rätt behörigheter som anges i följande tabell:

| Åtgärd                                                                     | Namn                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hämta nätverksgränssnitt                                     |
| Microsoft.Network/networkInterfaces/write                                  | Skapa eller uppdatera nätverksgränssnitt                        |
| Microsoft.Network/networkInterfaces/join/action                            | Koppla ett nätverksgränssnitt till en virtuell dator           |
| Microsoft.Network/networkInterfaces/delete                                 | Ta bort nätverksgränssnittet                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Ansluta till en resurs till ett nätverksgränssnitt via en servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Hämta network interface effektiva routningstabellen               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Hämta gränssnittet effektiva nätverkssäkerhetsgrupper           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hämta nätverksgränssnitt för belastningsutjämnare                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Hämta associationen för tjänsten                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Skapa eller uppdatera en tjänst-koppling                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Ta bort associationen för tjänsten                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Validera service association                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hämta IP-konfiguration nätverksgränssnitt                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en virtuell dator med flera nätverkskort med den [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en enda NIC VM med flera IPv4-adresser med hjälp av [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) eller [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Skapa en enda NIC VM med en privat IPv6-adress (bakom en belastningsutjämnare i Azure) med hjälp av den [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
- Skapa ett nätverksgränssnitt med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och använda [Azure princip](policy-samples.md) för virtuella nätverk