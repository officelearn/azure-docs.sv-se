---
title: Skapa, ändra eller ta bort ett Azure-nätverk-gränssnitt
titlesuffix: Azure Virtual Network
description: Lär dig vad ett nätverksgränssnitt är och hur du skapar, ändra inställningar för och ta bort ett.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: a3cb6e1b20e405cedddae8684a4b91fcb8a5514a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695353"
---
# <a name="create-change-or-delete-a-network-interface"></a>Skapa, ändra eller ta bort ett nätverksgränssnitt

Lär dig mer om att skapa, ändra inställningar för och ta bort ett nätverksgränssnitt. Ett nätverksgränssnitt kan en Azure virtuell dator ska kunna kommunicera med internet, Azure och lokala resurser. När du skapar en virtuell dator med Azure-portalen, skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Du kan i stället välja att skapa nätverksgränssnitt med anpassade inställningar och lägga till en eller flera nätverksgränssnitt till en virtuell dator när du skapar den. Du kanske också vill ändra standard gränssnitt för nätverksinställningar för en befintlig nätverksgränssnittet. Den här artikeln beskriver hur du skapar ett nätverksgränssnitt med anpassade inställningar, ändra befintliga inställningar, till exempel nätverk filter (nätverkssäkerhetsgrupp) tilldelning, undernättilldelning, DNS-serverinställningarna och IP-vidarebefordring och ta bort ett nätverksgränssnitt.

Om du behöver för att lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, se [hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du vill lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer kan du läsa [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

När du skapar en virtuell dator med Azure-portalen, skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Om du hellre vill ange alla gränssnitt nätverksinställningarna kan du skapa ett nätverksgränssnitt med anpassade inställningar och koppla nätverksgränssnittet till en virtuell dator när du skapar den virtuella datorn (med PowerShell eller Azure CLI). Du kan också skapa ett nätverksgränssnitt och lägga till den i en befintlig virtuell dator (med PowerShell eller Azure CLI). Om du vill lära dig mer om att skapa en virtuell dator med en befintlig nätverksgränssnitt eller att lägga till eller ta bort nätverksgränssnitt från befintliga virtuella datorer, se [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md). Innan du skapar ett nätverksgränssnitt måste du ha en befintlig [virtuellt nätverk](manage-virtual-network.md) i samma plats och prenumeration skapar du ett nätverksgränssnitt i.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj **+ Lägg till** under **nätverksgränssnitt**.
3. Ange eller Välj värden för följande inställningar och välj sedan **skapa**:

    |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Namnet måste vara unikt inom den resursgrupp som du väljer. Framöver kommer har du troligen flera nätverksgränssnitt i Azure-prenumerationen. Förslag när du skapar en namngivningskonvention att hantera flera nätverksgränssnitt enklare finns i [namngivningskonventioner](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Namnet kan inte ändras när nätverksgränssnittet har skapats.|
    |Virtuellt nätverk|Ja|Välj det virtuella nätverket för nätverksgränssnittet. Du kan endast tilldela ett nätverksgränssnitt i ett virtuellt nätverk som finns i samma prenumeration och plats som nätverksgränssnittet. När ett nätverksgränssnitt har skapats kan ändra du inte det virtuella nätverket som den är tilldelad till. Den virtuella datorn som du lägger till nätverksgränssnittet till måste även finnas på samma plats och prenumeration som nätverksgränssnittet.|
    |Undernät|Ja|Välj ett undernät i det virtuella nätverket som du har valt. Du kan ändra det undernät som nätverksgränssnittet har tilldelats när den har skapats.|
    |Tilldelning av privat IP-adress|Ja| I den här inställningen väljer du tilldelningsmetod för IPv4-adress. Välj av följande tilldelningsmetoder: **Dynamiska:** När du väljer det här alternativet, tilldelar Azure automatiskt nästa tillgängliga adress från adressutrymmet för det undernät som du har valt. **Statisk:** När du väljer det här alternativet måste du manuellt tilldela en tillgänglig IP-adress inom adressutrymmet för det undernät som du har valt. Statiska och dynamiska adresser ändras inte tills du ändrar dem eller nätverksgränssnittet har tagits bort. Du kan ändra tilldelningsmetoden när nätverkskortet har skapats. Azure DHCP-server tilldelar den här adressen till nätverksgränssnittet i operativsystemet för den virtuella datorn.|
    |Nätverkssäkerhetsgrupp|Nej| Lämna inställd **ingen**, Välj en befintlig [nätverkssäkerhetsgrupp](security-overview.md), eller [skapa en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md). Nätverkssäkerhetsgrupper kan du filtrera nätverkstrafik och från ett nätverksgränssnitt. Du kan använda noll eller en nätverkssäkerhetsgrupp för ett nätverksgränssnitt. Noll eller en säkerhetsgrupp i nätverket kan även tillämpas på det undernät som nätverksgränssnittet har tilldelats. När en nätverkssäkerhetsgrupp används för ett nätverksgränssnitt och undernät som nätverksgränssnittet har tilldelats, inträffa ibland oväntade resultat. Om du vill felsöka nätverkssäkerhetsgrupper för nätverksgränssnitt och undernät, se [felsöka nätverkssäkerhetsgrupper](diagnose-network-traffic-filter-problem.md).|
    |Prenumeration|Ja|Välj en av dina Azure [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Den virtuella datorn som du kan koppla ett nätverksgränssnitt för och det virtuella nätverket som du ansluter den till måste finnas i samma prenumeration.|
    |Privat IP-adress (IPv6)|Nej| Om du väljer den här kryssrutan måste tilldelas en IPv6-adress till nätverksgränssnittet, förutom IPv4-adress för nätverksgränssnittet. IPv6-avsnittet i den här artikeln för viktig information om användning av IPv6 med nätverksgränssnitt. Du kan inte välja en tilldelningsmetod för IPv6-adress. Om du vill tilldela en IPv6-adress tilldelas den med metoden dynamisk.
    |IPv6-namn (visas bara när den **privat IP-adress (IPv6)** är markerad) |Ja, om den **privat IP-adress (IPv6)** är markerad.| Det här namnet är tilldelad till en sekundär IP-konfiguration för nätverksgränssnittet. Läs mer om IP-konfigurationer i [Visa inställningar för nätverksgränssnittet](#view-network-interface-settings).|
    |Resursgrupp|Ja|Välj en befintlig [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eller skapa ett. Ett nätverksgränssnitt kan finnas i samma eller olika resursgruppen, den virtuella datorn som du ansluter den till, eller det virtuella nätverket som du ansluter den till.|
    |Location|Ja|Den virtuella datorn du har kopplat ett nätverksgränssnitt för och det virtuella nätverket som du ansluter den till måste finnas i samma [plats](https://azure.microsoft.com/regions), vilket även kallas en region.|

Portalen ger inte alternativet att tilldela en offentlig IP-adress till nätverksgränssnittet när du skapar den, även om portalen skapa en offentlig IP-adress och tilldela den till ett nätverksgränssnitt när du skapar en virtuell dator med hjälp av portalen. Läs hur du lägger till en offentlig IP-adress till nätverksgränssnittet när du har skapat den i [hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du vill skapa ett nätverksgränssnitt med en offentlig IP-adress, måste du använda CLI eller PowerShell för att skapa ett nätverksgränssnitt.

Portalen ger inte alternativet att tilldela nätverksgränssnittet till programsäkerhetsgrupper när du skapar ett nätverksgränssnitt, men Azure CLI och PowerShell. Du kan tilldela en befintlig nätverksgränssnitt till en programsäkerhetsgrupp med hjälp av portalen men så länge nätverksgränssnittet är ansluten till en virtuell dator. Läs hur du tilldelar ett nätverksgränssnitt till en programsäkerhetsgrupp i [Lägg till eller ta bort från programsäkerhetsgrupper](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure tilldelar en MAC-adress till nätverksgränssnittet när nätverksgränssnittet är kopplad till en virtuell dator och den virtuella datorn startas första gången. Du kan inte ange MAC-adress som Azure tilldelar till nätverksgränssnittet. MAC-adressen förblir tilldelad till nätverksgränssnittet tills nätverksgränssnittet har tagits bort eller privata IP-adress som tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet ändras. Läs mer om IP-adresser och IP-konfigurationer i [hantera IP-adresser](virtual-network-network-interface-addresses.md)

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Visa inställningar för nätverksgränssnittet

Du kan visa och ändra de flesta inställningar för ett nätverksgränssnitt när den har skapats. Portalen visar inte DNS-suffix eller program medlemskap i säkerhetsgruppen för nätverksgränssnittet. Du kan använda PowerShell eller Azure CLI [kommandon](#view-settings-commands) att visa DNS-suffix och programmet medlemskap i säkerhetsgruppen.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnittet som du vill visa eller ändra inställningar för i listan.
3. Följande objekt visas i listan för nätverksgränssnittet som du har valt:
   - **Översikt:** Innehåller information om nätverksgränssnittet, till exempel IP-adresserna som tilldelats, virtuellt nätverk/undernät nätverksgränssnittet har tilldelats och den virtuella datorn nätverksgränssnittet är kopplad till (om den är ansluten till en). Följande bild visar en översikt över inställningar för ett nätverksgränssnitt med namnet **mywebserver256**: ![Översikt över Network-gränssnitt](./media/virtual-network-network-interface/nic-overview.png)

     Du kan flytta ett nätverksgränssnitt till en annan resursgrupp eller prenumeration genom att välja (**ändra**) bredvid den **resursgrupp** eller **prenumerationsnamn**. Om du flyttar ett nätverksgränssnitt måste du flytta alla resurser som är relaterade till nätverksgränssnittet med den. Om nätverksgränssnittet är kopplat till en virtuell dator, till exempel måste du också flytta den virtuella datorn och andra VM-relaterade resurser. Om du vill flytta ett nätverksgränssnitt, se [flytta resursen till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Artikeln innehåller förutsättningar och hur du flyttar resurser med hjälp av Azure portal, PowerShell och Azure CLI.
   - **IP-konfigurationer:** Offentliga och privata IPv4 och IPv6-adresser tilldelade till IP-konfigurationer visas här. Om en IPv6-adress har tilldelats en IP-konfiguration, visas inte adressen. Läs mer om IP-konfigurationer och hur du lägger till och ta bort IP-adresser i [konfigurera IP-adresser för en Azure nätverksgränssnittet](virtual-network-network-interface-addresses.md). IP-vidarebefordring och undernättilldelning konfigureras också i det här avsnittet. Mer information om dessa inställningar finns [aktivera eller inaktivera IP-vidarebefordring](#enable-or-disable-ip-forwarding) och [ändra undernättilldelning](#change-subnet-assignment).
   - **DNS-servrar:** Du kan ange vilken DNS-server som ett nätverksgränssnitt tilldelas av Azure DHCP-servrar. Nätverksgränssnittet kan ärver inställningarna från det virtuella nätverket nätverksgränssnittet har tilldelats eller har en anpassad inställning som åsidosätter inställning för det virtuella nätverket som den är tilldelad till. Om du vill ändra vad som visas, se [ändra DNS-servrar](#change-dns-servers).
   - **Nätverkssäkerhetsgrupp (NSG):** Visar vilket NSG är associerad med nätverksgränssnittet (om sådan finns). En NSG innehåller inkommande och utgående regler för att filtrera nätverkstrafik för nätverksgränssnittet. Om en NSG är associerad med nätverksgränssnittet, visas namnet på den associera NSG: N. Om du vill ändra vad som visas, se [koppla eller koppla bort en nätverkssäkerhetsgrupp](#associate-or-dissociate-a-network-security-group).
   - **Egenskaper:** Visar viktiga inställningar för nätverksgränssnittet, inklusive MAC-adressen (tomt om nätverksgränssnittet inte är kopplad till en virtuell dator) och prenumerationen som den finns i.
   - **Gällande säkerhetsregler:**  Säkerhetsregler visas om nätverksgränssnittet är ansluten till en aktiv virtuell dator och en NSG är associerad till nätverksgränssnittet, det undernät som den är tilldelad till eller båda. Läs mer om vad som visas i [visa gällande säkerhetsregler](#view-effective-security-rules). Mer information om Nätverkssäkerhetsgrupper finns [Nätverkssäkerhetsgrupper](security-overview.md).
   - **Effektiva vägar:** Vägar listas om nätverksgränssnittet är kopplat till en aktiv virtuell dator. Vägar är en kombination av standardvägar som Azure, några användardefinierade vägar och BGP-vägar som kan finnas för det undernät som nätverksgränssnittet har tilldelats. Läs mer om vad som visas i [visa effektiva vägar](#view-effective-routes). Läs mer om Azure standardvägar och användardefinierade vägar i [routningsöversikten](virtual-networks-udr-overview.md).
   - **Vanliga Azure Resource Manager-inställningar:**  Läs mer om vanliga Azure Resource Manager-inställningar i [aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md), [åtkomstkontroll (IAM)](../role-based-access-control/overview.md), [taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [låser](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), och [ Automationsskript](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

<a name="view-settings-commands"></a>**Kommandon**

Om en IPv6-adress tilldelas till ett nätverksgränssnitt, returnerar det faktum att adressen tilldelas, men den inte returnerar den tilldelade adressen i PowerShell-utdata. På samma sätt CLI returnerar det faktum att adressen är tilldelad, men returnerar *null* i dess utdata för adressen.

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic list](/cli/azure/network/nic) att visa nätverksgränssnitt i prenumerationen; [az network nic show](/cli/azure/network/nic) att visa inställningar för ett nätverksgränssnitt|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) visa nätverksgränssnitt i prenumeration eller visa inställningarna för ett nätverksgränssnitt|

## <a name="change-dns-servers"></a>Ändra DNS-servrar

DNS-servern är tilldelad av Azure DHCP-servern till nätverksgränssnittet i VM-operativsystem. DNS-server som tilldelats är det DNS-Serverinställningen för ett nätverksgränssnitt. Läs mer om inställningarna för matchning av namn för ett nätverksgränssnitt i [namnmatchning för virtuella datorer](virtual-networks-name-resolution-for-vms-and-role-instances.md). Nätverksgränssnittet kan ärva inställningar från det virtuella nätverket eller använda en egen unik inställningar som åsidosätter inställning för det virtuella nätverket.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj det nätverksgränssnitt som du vill ändra en DNS-server för i listan.
3. Välj **DNS-servrar** under **inställningar**.
4. Välj antingen:
   - **Ärv från virtuellt nätverk**: Välj det här alternativet att ärva DNS-Serverinställningen som definierats för det virtuella nätverket nätverksgränssnittet har tilldelats. En anpassad DNS-server eller Azure-tillhandahållna DNS-server har definierats på nivån för virtuella nätverket. Azure-tillhandahållna DNS-servern kan matcha värdnamnen för resurser som är tilldelade till samma virtuella nätverk. FQDN måste användas för att matcha för resurser som tilldelats olika virtuella nätverk.
   - **Anpassat**: Du kan konfigurera en egen DNS-server för att matcha namn i flera virtuella nätverk. Ange IP-adressen för den server som du vill använda som en DNS-server. DNS-serveradressen som du anger tilldelas endast till det här nätverksgränssnittet och åsidosättningar DNS-inställningar för det virtuella nätverket nätverksgränssnittet har tilldelats.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivera eller inaktivera IP-vidarebefordring

IP-vidarebefordran gör det möjligt för den virtuella datorn ett nätverksgränssnitt som är kopplad till:
- Ta emot nätverkstrafik som inte är avsedda för en av IP-adresser som har tilldelats någon IP-konfigurationer som tilldelats till nätverksgränssnittet.
- Skicka nätverkstrafik med en annan IP-källadress än den som tilldelas till en IP-konfigurationer för ett nätverksgränssnitt.

Inställningen måste aktiveras för varje nätverksgränssnitt som är kopplad till den virtuella datorn som tar emot trafik som den virtuella datorn ska vidarebefordra. En virtuell dator kan vidarebefordra trafik, oavsett om den har flera nätverksgränssnitt eller ett enda nätverksgränssnitt som är kopplade till den. IP-vidarebefordring är en Azure-inställning, måste den virtuella datorn också att köra ett program som kan vidarebefordra trafik, till exempel brandvägg, WAN-optimering och program för belastningsutjämning. När en virtuell dator körs nätverksprogram, är den virtuella datorn ofta kallas ett nätverks virtuella installation. Du kan visa en lista över redo att distribuera virtuella nätverksutrustning i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-vidarebefordring används vanligtvis med användardefinierade vägar. Mer information om användardefinierade vägar finns [användardefinierade vägar](virtual-networks-udr-overview.md).

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnittet som du vill aktivera eller inaktivera IP-vidarebefordran för.
3. Välj **IP-konfigurationer** i den **inställningar** avsnittet.
4. Välj **aktiverad** eller **inaktiverad** (standardinställning) att ändra inställningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Ändra undernättilldelning

Du kan ändra undernätet, men inte det virtuella nätverket, som ett nätverksgränssnitt har tilldelats.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj det nätverksgränssnitt som du vill ändra undernättilldelning för.
3. Välj **IP-konfigurationer** under **inställningar**. Om alla privata IP-adresser för alla IP-konfigurationer har **(statiska)** bredvid dem, måste du ändra tilldelningsmetoden för IP-adress till dynamisk genom att följa stegen nedan. Alla privata IP-adresser måste tilldelas med dynamiska tilldelningsmetoden ändra tilldelningen för undernätet för nätverksgränssnittet. Om adresserna som är tilldelade med metoden dynamisk, fortsätter du till steg fem. Om alla IPv4-adresser tilldelas med statisk tilldelningsmetod, utför följande steg om du vill ändra tilldelningsmetoden till dynamisk:
   - Välj IP-konfiguration som du vill ändra tilldelningsmetoden för IPv4-adress i listan över IP-konfigurationer.
   - Välj **dynamisk** för privata IP-adress **tilldelning** metod. Du kan inte tilldela en IPv6-adress med statisk tilldelningsmetod.
   - Välj **Spara**.
4. Välj det undernät som du vill flytta nätverksgränssnitt för från den **undernät** listrutan.
5. Välj **Spara**. Nya dynamiska adresser tilldelas från adressintervallet i undernätet för det nya undernätet. När du har tilldelat nätverksgränssnittet till ett nytt undernät, kan du tilldela en statisk IPv4-adress från det nya adressintervallet för undernätet om du väljer. Läs mer om att lägga till, ändra och ta bort IP-adresser för ett nätverksgränssnitt i [hantera IP-adresser](virtual-network-network-interface-addresses.md).

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Lägg till eller ta bort från programsäkerhetsgrupper

Du kan bara lägga till ett nätverksgränssnitt för eller ta bort ett nätverksgränssnitt från en programsäkerhetsgrupp med hjälp av portalen om nätverksgränssnittet är kopplat till en virtuell dator. Du kan använda PowerShell eller Azure CLI för att lägga till ett nätverksgränssnitt för eller ta bort ett nätverksgränssnitt från en programsäkerhetsgrupp om nätverksgränssnittet är ansluten till en virtuell dator eller inte. Läs mer om [programsäkerhetsgrupper](security-overview.md#application-security-groups) och hur du [skapa en programsäkerhetsgrupp](manage-network-security-group.md).

1. I den *Sök efter resurser, tjänster och dokument* rutan längst upp i portalen, börjar du skriva namnet på en virtuell dator som har ett nätverksgränssnitt som du vill lägga till eller ta bort från en programsäkerhetsgrupp. När namnet på den virtuella datorn visas i sökresultatet väljer du den.
2. Under **INSTÄLLNINGAR** väljer du **Nätverk**.  Välj **konfigurera programmet säkerhetsgrupper**väljer programsäkerhetsgrupper som du vill lägga till nätverksgränssnittet till, eller avmarkera programsäkerhetsgrupper som du vill ta bort nätverksgränssnittet från, Välj sedan **spara**. Endast nätverksgränssnitt som finns i samma virtuella nätverk kan läggas till i säkerhetsgruppen för samma program. Programsäkerhetsgruppen måste finnas på samma plats som nätverksgränssnittet.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Koppla eller koppla bort en grupp

1. Ange i sökrutan överst på portalen *nätverksgränssnitt* i sökrutan. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnittet i listan som du vill associera en nätverkssäkerhetsgrupp till eller koppla bort en grupp från.
3. Välj **nätverkssäkerhetsgrupp** under **inställningar**.
4. Välj **Redigera**.
5. Välj **nätverkssäkerhetsgrupp** och välj sedan den grupp du vill koppla till nätverksgränssnittet eller välj **ingen**, för att koppla bort en grupp.
6. Välj **Spara**.

**Kommandon**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Ta bort ett nätverksgränssnitt

Du kan ta bort ett nätverksgränssnitt så länge det inte är kopplat till en virtuell dator. Om ett nätverksgränssnitt är kopplat till en virtuell dator, måste du först placera den virtuella datorn i tillståndet Stoppad (frigjord) sedan ta bort nätverksgränssnittet från den virtuella datorn. Om du vill ta bort en nätverksgränssnittet från en virtuell dator, utför du stegen i [koppla från ett nätverksgränssnitt från en virtuell dator](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Du kan inte koppla från ett nätverksgränssnitt från en virtuell dator om det är den enda nätverksgränssnitt som är kopplat till den virtuella datorn men. En virtuell dator måste alltid ha minst ett nätverksgränssnitt som är kopplat till den. Tar bort en virtuell dator kopplar från alla nätverksgränssnitt som är kopplade till den, men tar inte bort nätverksgränssnitt.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj **...**  på höger sida av nätverksgränssnitt som du vill ta bort från listan över nätverksgränssnitt.
3. Välj **Ta bort**.
4. Välj **Ja** att bekräfta borttagningen av ett nätverksgränssnitt.

När du tar bort ett nätverksgränssnitt släpps alla MAC- eller IP-adresser som är tilldelade till den.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Lösa problem med nätverksanslutningen

Om du inte kan kommunicera till eller från en virtuell dator, security regler för nätverkssäkerhetsgrupper och vägar för ett nätverksgränssnitt kan vara orsaken till problemet. Du har följande alternativ för att lösa problemet:

### <a name="view-effective-security-rules"></a>Visa effektiva säkerhetsregler

De effektiva säkerhetsreglerna för varje nätverksgränssnitt som är kopplat till en virtuell dator är en kombination av de regler som du har skapat i en nätverkssäkerhetsgrupp och [standardsäkerhetsregler](security-overview.md#default-security-rules). Förstå de effektiva säkerhetsreglerna för ett nätverksgränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera till eller från en virtuell dator. Du kan visa gällande reglerna för alla nätverksgränssnitt som är kopplad till en aktiv virtuell dator.

1. Ange namnet på en virtuell dator som du vill visa gällande säkerhetsregler för i sökrutan överst på portalen. Om du inte vet namnet på en virtuell dator, ange *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten, markerar du det och sedan välja en virtuell dator i listan.
2. Välj **nätverk** under **inställningar**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **gällande säkerhetsregler** under **stöd + felsökning**.
5. Granska listan över gällande säkerhetsregler för att avgöra om det finns rätt reglerna för din nödvändiga inkommande och utgående kommunikation. Mer information om vad som visas i listan i [översikt över Network security group](security-overview.md).

IP-flöde Kontrollera funktion i Azure Network Watcher kan också hjälpa dig att avgöra om säkerhetsregler förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Mer information finns i [IP-flödesverifieringen](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Visa effektiva vägar

Gällande routningar för nätverksgränssnitt som är kopplade till en virtuell dator är en kombination av standardvägar och alla vägar som du har skapat alla vägar som sprids från lokala nätverk via BGP via en gateway för virtuellt Azure-nätverk. Förstå de effektiva vägarna för ett nätverksgränssnitt kan hjälpa dig att avgöra varför du inte kan kommunicera till eller från en virtuell dator. Du kan visa de effektiva vägarna för alla nätverksgränssnitt som är kopplad till en aktiv virtuell dator.

1. Ange namnet på en virtuell dator som du vill visa gällande säkerhetsregler för i sökrutan överst på portalen. Om du inte vet namnet på en virtuell dator, ange *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten, markerar du det och sedan välja en virtuell dator i listan.
2. Välj **nätverk** under **inställningar**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **gällande routningar** under **stöd + felsökning**.
5. Granska listan över gällande routningar för att avgöra om det finns rätt vägar för ditt nödvändiga inkommande och utgående kommunikation. Mer information om vad som visas i listan i [routningsöversikten](virtual-networks-udr-overview.md).

Nästa hopp-funktionen i Azure Network Watcher kan också hjälpa dig att avgöra om vägar förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Mer information finns i [nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Behörigheter

För att utföra uppgifter i nätverksgränssnitt måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som är tilldelad behörigheten som visas i följande tabell:

| Åtgärd                                                                     | Namn                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hämta nätverksgränssnitt                                     |
| Microsoft.Network/networkInterfaces/write                                  | Skapa eller uppdatera nätverksgränssnittet                        |
| Microsoft.Network/networkInterfaces/join/action                            | Koppla ett nätverksgränssnitt till en virtuell dator           |
| Microsoft.Network/networkInterfaces/delete                                 | Ta bort nätverksgränssnitt                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Ansluta till en resurs till ett nätverksgränssnitt via en servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Hämta effektiva routningstabellen för nätverk-gränssnitt               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Hämta gränssnittet effektiva nätverkssäkerhetsgrupper           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hämta belastningsutjämnare för nätverk-gränssnitt                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Hämta associationen för tjänsten                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Skapa eller uppdatera en tjänst-koppling                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Ta bort associationen för tjänsten                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Verifiera service association                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hämta IP-konfiguration av nätverksgränssnitt                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en virtuell dator med flera nätverkskort med hjälp av den [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en enda NIC VM med flera IPv4-adresser som använder den [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) eller [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Skapa en enda NIC-VM med en privat IPv6-adress (bakom en belastningsutjämnare för Azure) med hjälp av den [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en network interface med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mall](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk
