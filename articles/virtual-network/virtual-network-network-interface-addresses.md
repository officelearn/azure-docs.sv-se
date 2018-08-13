---
title: Konfigurera IP-adresser för en Azure nätverksgränssnitt | Microsoft Docs
description: Lär dig mer om att lägga till, ändra och ta bort privata och offentliga IP-adresser för ett nätverksgränssnitt.
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
ms.openlocfilehash: 7fe4fdbf6c6b3cbbd6d01ef5309699c3d3991d53
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003822"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Lägga till, ändra eller ta bort IP-adresser för ett gränssnitt för Azure-nätverk

Lär dig mer om att lägga till, ändra och ta bort offentliga och privata IP-adresser för ett nätverksgränssnitt. Privata IP-adresser som tilldelas ett nätverksgränssnitt kan en virtuell dator ska kunna kommunicera med andra resurser i en Azure-nätverk och anslutna nätverk. En privat IP-adress kan också utgående kommunikation till Internet med en oförutsägbara IP-adress. En [offentliga IP-adressen](virtual-network-public-ip-address.md) tilldelad till ett nätverksgränssnittet aktiverar inkommande kommunikation till en virtuell dator från Internet. Adressen kan också utgående kommunikation från den virtuella datorn till Internet med en förutsägbar IP-adress. Mer information finns i [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Om du behöver för att skapa, ändra eller ta bort ett nätverksgränssnitt, läsa den [hantera ett nätverksgränssnitt](virtual-network-network-interface.md) artikeln. Om du vill lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från en virtuell dator kan läsa den [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md) artikeln.

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudiekursen kräver Azure PowerShell-modulen version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [nätverk gränssnitt behörigheter](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Lägg till IP-adresser

Du kan lägga till så många [privata](#private) och [offentliga](#public) [IPv4](#ipv4) adresser som behövs för att ett nätverksgränssnitt, inom de gränser som anges i den [förAzure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikeln. Du kan inte använda portalen för att lägga till en IPv6-adress till en befintlig nätverksgränssnitt (även om du kan använda portalen för att lägga till en privat IPv6-adress till ett nätverksgränssnitt när du skapar ett nätverksgränssnitt). Du kan använda PowerShell eller CLI för att lägga till en privat IPv6-adress till en [sekundära IP-konfiguration](#secondary) (så länge det finns inga befintliga sekundära IP-konfigurationer) för en befintlig nätverksgränssnitt som inte är kopplad till en virtuell dator. Du kan inte använda ett verktyg för att lägga till en offentlig IPv6-adress till ett nätverksgränssnitt. Se [IPv6](#ipv6) mer information om hur du använder IPv6-adresser.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnittet som du vill lägga till en IPv4-adress för i listan.
3. Under **inställningar**väljer **IP-konfigurationer**.
4. Under **IP-konfigurationer**väljer **+ Lägg till**.
5. Anger du följande och välj sedan **OK**:

    |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Måste vara unikt för nätverksgränssnittet|
    |Typ|Ja|Eftersom du lägger till en IP-konfiguration till en befintlig nätverksgränssnitt och varje nätverksgränssnitt måste ha en [primära](#primary) IP-konfiguration, det enda alternativet är **sekundära**.|
    |Tilldelningsmetoden för privat IP-adress|Ja|[**Dynamisk**](#dynamic): Azure tilldelar nästa tillgängliga adress för adressintervall för undernätet nätverksgränssnittet har distribuerats i. [**Statisk**](#static): du tilldelar en adress som är oanvända för adressintervall för undernätet nätverksgränssnittet har distribuerats i.|
    |Offentlig IP-adress|Nej|**Inaktiverat:** ingen offentlig IP-adressresurs är för närvarande associerad till IP-konfigurationen. **Aktiverad:** väljer du en befintlig offentlig IPv4-IP-adress, eller skapa en ny. Läs hur du skapar en offentlig IP-adress, den [offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address) artikeln.|
6. Lägg till manuellt sekundära privata IP-adresser för VM-operativsystemet när du har slutfört instruktionerna i den [tilldela flera IP-adresser till VM-operativsystem](virtual-network-multiple-ip-addresses-portal.md#os-config) artikeln. Se [privata](#private) IP-adresser för att tänka på innan du manuellt lägger till IP-adresser till ett VM-operativsystem. Lägg inte till offentliga IP-adresser till VM-operativsystem.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Ändra inställningar för IP-adresser

Du kanske måste ändra tilldelningsmetoden för en IPv4-adress ändra statiska IPv4-adress, eller ändra offentliga IP-adress som tilldelats ett nätverksgränssnitt. Om du ändrar den privata IPv4-adressen för en sekundär IP-konfiguration som är associerad med ett sekundärt nätverksgränssnitt på en virtuell dator (Läs mer om [primära och sekundära nätverksgränssnitt](virtual-network-network-interface-vm.md)), placera den virtuella datorn i ett stoppat (frigjort) tillstånd innan du slutför följande steg:

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj nätverksgränssnittet som du vill visa eller ändra inställningar för IP-adresser i listan.
3. Under **inställningar**väljer **IP-konfigurationer**.
4. Välj IP-konfiguration som du vill ändra i listan.
5. Ändra inställningar, som du vill, med information om inställningarna i steg 5 i [lägga till en IP-konfiguration](#add-ip-addresses).
6. Välj **Spara**.

>[!NOTE]
>Om det primära nätverksgränssnittet har flera IP-konfigurationer och du ändrar den privata IP-adressen för den primära IP-konfigurationen, måste du manuellt tilldela de primära och sekundära IP-adresserna till nätverksgränssnittet i Windows (krävs inte för Linux) . För att manuellt tilldela IP-adresser till ett nätverksgränssnitt i ett operativsystem, se [tilldela flera IP-adresser till virtuella datorer](virtual-network-multiple-ip-addresses-portal.md#os-config). Att tänka på innan du manuellt lägger till IP-adresser till ett VM-operativsystem, se [privata](#private) IP-adresser. Lägg inte till offentliga IP-adresser till VM-operativsystem.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Ta bort IP-adresser

Du kan ta bort [privata](#private) och [offentliga](#public) IP-adresser från ett nätverksgränssnitt, men ett nätverksgränssnitt måste alltid ha minst en privat IPv4-adress tilldelas till den.

1. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten, markerar du den.
2. Välj det nätverksgränssnitt som du vill ta bort IP-adresser i listan.
3. Under **inställningar**väljer **IP-konfigurationer**.
4. Höger-Välj en [sekundära](#secondary) IP-konfiguration (du kan inte ta bort den [primära](#primary) configuration) som du vill ta bort, väljer **ta bort**och välj sedan ** Ja**, för att bekräfta borttagningen. Om konfigurationen har haft en offentlig IP-adressresurs som är associerade med den, resursen har kopplats bort från IP-konfiguration, men resursen tas inte bort.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ network nic ip-config delete](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-konfigurationer

[Privata](#private) och (frivilligt) [offentliga](#public) IP-adresser tilldelas till en eller flera IP-konfigurationer som tilldelats ett nätverksgränssnitt. Det finns två typer av IP-konfigurationer:

### <a name="primary"></a>Primär

Varje nätverksgränssnitt tilldelas en primär IP-konfiguration. En primär IP-konfiguration:

- Har en [privata](#private) [IPv4](#ipv4) adress som tilldelats till den. Du kan inte tilldela en privat [IPv6](#ipv6) adress till en primär IP-konfiguration.
- Kan också ha en [offentliga](#public) IPv4-adress som tilldelats. Du kan inte tilldela en offentlig IPv6-adress till en primär eller sekundär IP-konfigurationen. Men du kan tilldela en offentlig IPv6-adress till en Azure belastningsutjämnare som kan läsa in belastningsutjämna trafik till en virtuell dators privata IPv6-adress. Mer information finns i [information och begränsningar för IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Sekundär

Förutom en primär IP-konfiguration, kan ett nätverksgränssnitt ha noll eller flera sekundära IP-konfigurationer tilldelade till den. En sekundär IP-konfiguration:

- Ha måste en privat IPv4- eller IPv6-adress tilldelad till den. Om adressen är IPv6, kan nätverksgränssnittet bara ha en sekundär IP-konfiguration. Om adressen är IPv4, kan nätverksgränssnittet ha flera sekundära IP-konfigurationer tilldelade till den. Läs mer om hur många privata och offentliga IPv4-adresser kan tilldelas till ett nätverksgränssnitt i den [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikeln.
- Också kan ha en offentlig IPv4-adress tilldelad till sig om den privata IP-adressen är IPv4. Om den privata IP-adressen är IPv6, kan du tilldela en offentlig IPv4- eller IPv6-adress till IP-konfigurationen. Tilldela flera IP-adresser till ett nätverksgränssnitt är användbart i scenarier som:
    - Du kan hantera flera webbplatser eller tjänster med olika IP-adresser och SSL-certifikat på en enda server.
    - En virtuell dator som fungerar som en virtuell nätverksenhet, till exempel en brandvägg eller belastningsutjämnare.
    - Möjligheten att lägga till någon av de privata IPv4-adresserna för alla nätverksgränssnitt i en Azure Load Balancer-backend-poolen. Tidigare kunde endast primär IPv4-adressen för det primära nätverksgränssnittet läggas till en backend-poolen. Läs mer om hur du belastningsutjämnar flera IPv4-konfigurationer i den [belastningsutjämning flera IP-konfigurationer](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln. 
    - Möjligheten att läsa in balansera en IPv6-adress för ett nätverksgränssnitt. Läs mer om hur du belastningsutjämnar till en privat IPv6-adress i den [belastningsutjämna IPv6-adresser](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln.

## <a name="address-types"></a>-Adresstyper

Du kan tilldela följande typer av IP-adresser till ett [IP-konfiguration](#ip-configurations):

### <a name="private"></a>Privat

Privata [IPv4](#ipv4) adresser låta en virtuell dator ska kunna kommunicera med andra resurser i ett virtuellt nätverk eller andra anslutna nätverk. En virtuell dator kan inte förmedlas inkommer till, och inte heller kan den virtuella datorn kommunicera utgående med en privat [IPv6](#ipv6) adress, med ett undantag. En virtuell dator kan kommunicera med Azure load balancer med IPv6-adress. Mer information finns i [information och begränsningar för IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Som standard tilldelar Azure DHCP-servrar den offentliga IPv4-adressen för den [primära IP-konfigurationen](#primary) för Azure nätverksgränssnittet till nätverksgränssnittet i VM-operativsystem. Om inte behövs bör du aldrig manuellt ange IP-adressen för ett nätverksgränssnitt i den virtuella datorns operativsystem.

> [!WARNING]
> Om IPv4-adress som angetts som primär IP-adress för ett nätverksgränssnitt i en virtuell dators operativsystem kopplas någonsin skiljer sig från den privat IPv4-adress som tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet till en virtuell dator i Azure kan förlora du anslutningen till den virtuella datorn.

Det finns scenarier där det är nödvändigt att ange IP-adressen för ett nätverksgränssnitt i den virtuella datorns operativsystem manuellt. Du måste till exempel manuellt ange primära och sekundära IP-adresserna för en Windows-operativsystemet när du lägger till flera IP-adresser till virtuella Azure-datorer. För en Linux-dator, kan du bara behöva manuellt ange sekundära IP-adresser. Se [Lägg till IP-adresser till ett VM-operativsystem](virtual-network-multiple-ip-addresses-portal.md#os-config) mer information. Om du behöver ändra den adress som tilldelats en IP-konfiguration, det vi rekommenderar att du:

1. Se till att den virtuella datorn tar emot en adress från Azure DHCP-servrar. När du har kan du ändra tilldelningen av IP-adressen till DHCP i operativsystemet och starta om den virtuella datorn.
2. Stoppa (frigöra) den virtuella datorn.
3. Ändra IP-adress för IP-konfiguration i Azure.
4. Startar den virtuella datorn.
5. [Konfigurera manuellt](virtual-network-multiple-ip-addresses-portal.md#os-config) sekundära IP-adresser inom operativsystemet (och även den primära IP-adressen i Windows) så att de matchar vad du ställer in i Azure.

Genom att följa förblir de här stegen, privata IP-adress tilldelas till nätverksgränssnitt i Azure och inom en virtuell dators operativsystem, desamma. Om du vill hålla reda på vilka virtuella datorer i din prenumeration som du har angett IP-adresser inom ett operativsystem för manuellt, Överväg att lägga till en Azure [taggen](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) till de virtuella datorerna. Du kan använda ”IP-adresstilldelning: statisk”, till exempel. På så sätt kan du lätt kan hitta de virtuella datorerna i din prenumeration som du har ställt in manuellt IP-adress för operativsystemet.

Förutom att aktivera en virtuell dator ska kunna kommunicera med andra resurser i samma eller anslutna virtuella nätverk, kan en privat IP-adress också en virtuell dator kommunicera utgående till Internet. Utgående anslutningar är adress källnätverket översättas av Azure till en oförutsägbara offentliga IP-adress. Om du vill veta mer om Azure utgående Internet-anslutning kan du läsa den [Azure utgående Internet-anslutning](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln. Du kan inte kommunicera inkommande från Internet till en virtuell dators privata IP-adress. Om din utgående anslutningar kräver en förutsägbar offentliga IP-adress kan du associera en offentlig IP-adressresurs till ett nätverksgränssnitt.

### <a name="public"></a>Offentligt

Offentliga IP-adresser som tilldelats via en offentlig IP-adressresurs aktivera inkommande anslutningar till en virtuell dator från Internet. Utgående anslutningar till Internet använder en förutsägbar IP-adress. Se [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mer information. Du kan tilldela en offentlig IP-adress till en IP-konfiguration, men inte behöver. Om du inte tilldelar en offentlig IP-adress till en virtuell dator genom att associera en offentlig IP-adressresurs, kan den virtuella datorn fortfarande kommunicera utgående till Internet. I det här fallet är den privata IP-adressen adress källnätverket översättas av Azure till en oförutsägbara offentliga IP-adress. Läs mer om offentliga IP-adressresurser i [offentliga IP-adressresurs](virtual-network-public-ip-address.md).

Det finns gränser för antalet privata och offentliga IP-adresser som du kan tilldela till ett nätverksgränssnitt. Mer information finns i [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikeln.

> [!NOTE]
> Azure omvandlar en virtuell dators privata IP-adressen till en offentlig IP-adress. En virtuell dators operativsystem är därför ovetande om eventuella offentlig IP-adress som tilldelats, så du behöver inte någonsin manuellt tilldela en offentlig IP-adress i operativsystemet.

## <a name="assignment-methods"></a>Tilldelningsmetoder

Offentliga och privata IP-adresser tilldelas med någon av följande tilldelningsmetoder:

### <a name="dynamic"></a>Dynamisk

Dynamisk privat IPv4 och IPv6 (valfritt) adresser att tilldelas som standard.

- **Endast offentliga**: Azure tilldelar adress från ett intervall som är unika för varje Azure-region. Läs vilka adressintervall som är kopplade till varje region i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Adressen kan ändras när en virtuell dator är stoppad (frigjord), sedan startas igen. Du kan inte tilldela en IP-konfiguration med hjälp av antingen tilldelningsmetod en offentlig IPv6-adress.
- **Endast privat**: Azure reserverar de fyra första adresserna i varje undernätsadressintervall och tilldelar inte adresserna. Azure tilldelar nästa tillgängliga adress till en resurs från adressintervallet i undernätet. Om till exempel undernätets adressintervall är 10.0.0.0/16, och adresserna 10.0.0.0.4-10.0.0.14 redan har tilldelats (.0-.3 är reserverade) tilldelar Azure 10.0.0.15 till resursen. Dynamisk är standardinställningen för allokering. När de har tilldelats släpps endast dynamiska IP-adresser om ett nätverksgränssnitt har tagits bort, tilldelats ett annat undernät inom samma virtuella nätverk eller om allokeringsmetoden ändras till statisk och en annan IP-adress anges. Som standard tilldelar Azure den tidigare dynamiskt tilldelade adressen som statisk adress när du ändrar allokeringsmetod från dynamisk till statisk. Du kan endast tilldela en privat IPv6-adress med hjälp av metoden dynamisk tilldelning.

### <a name="static"></a>Statisk

Du kan tilldela en offentlig eller privat statisk IPv4-adress (valfritt) till en IP-konfiguration. Du kan inte tilldela en statisk offentlig eller privat IPv6-adress till en IP-konfiguration. Läs mer om hur Azure tilldelar statiska offentliga IPv4-adresser i [offentliga IP-adresser](virtual-network-public-ip-address.md).

- **Endast offentliga**: Azure tilldelar adress från ett intervall som är unika för varje Azure-region. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064). Adressen ändras inte förrän den offentliga IP-adressresursen som den är tilldelad till har tagits bort eller tilldelningsmetoden ändras till dynamisk. Om den offentliga IP-adressresursen är kopplad till en IP-konfiguration, måste den kopplas bort från IP-konfiguration innan du ändrar dess tilldelningsmetod.
- **Endast privat**: du väljer och tilldelar en adress från undernätets adressintervall. Adressen som du tilldelar kan vara alla adresser i adressintervallet i undernätet som inte är någon av de fyra första adresserna i undernätets adressintervall och som inte är tilldelade till någon annan resurs i undernätet. Statiska adresser släpps endast om ett nätverksgränssnitt tas bort. Om du ändrar allokeringsmetoden till statisk tilldelar Azure dynamiskt de tidigare tilldelade statiska IP-adresserna som dynamiska adresser, även om adressen inte är nästa tillgängliga adress i undernätets adressintervall. Adressen ändras också om nätverksgränssnittet har tilldelats ett annat undernät i samma virtuella nätverk. För att tilldela nätverksgränssnittet till ett annat undernät måste du emellertid först ändra allokeringsmetoden från statisk till dynamisk. När nätverksgränssnittet har tilldelats ett annat undernät kan du ändra allokeringsmetod till statisk och tilldela en IP-adress från det nya undernätets adressintervall.

## <a name="ip-address-versions"></a>IP-adress versioner

När du tilldelar adresser kan du ange följande versioner:

### <a name="ipv4"></a>IPv4

Varje nätverksgränssnitt måste ha ett [primära](#primary) IP-konfiguration med en tilldelad [privata](#private) [IPv4](#ipv4) adress. Du kan lägga till en eller flera [sekundära](#secondary) IP-konfigurationer som har en privat IPv4 och (frivilligt) en IPv4 [offentliga](#public) IP-adress.

### <a name="ipv6"></a>IPv6

Du kan tilldela noll eller en privat [IPv6](#ipv6) adress till en sekundär IP-konfiguration för ett nätverksgränssnitt. Ett nätverksgränssnitt kan inte ha någon befintlig sekundära IP-konfigurationer. Du kan inte lägga till en IP-konfiguration med en IPv6-adress med hjälp av portalen. Använd PowerShell eller CLI för att lägga till en IP-konfiguration med en privat IPv6-adress till en befintlig nätverksgränssnitt. Ett nätverksgränssnitt kan inte kopplas till en befintlig virtuell dator.

> [!NOTE]
> Om du kan skapa ett nätverksgränssnitt med en IPv6-adress med hjälp av portalen, du kan inte lägga till en befintlig nätverksgränssnitt till en ny eller befintlig virtuell dator med hjälp av portalen. Använda PowerShell eller Azure CLI 2.0 för att skapa ett nätverksgränssnitt med en privat IPv6-adress och bifogar nätverksgränssnittet när du skapar en virtuell dator. Du kan inte koppla ett nätverksgränssnitt med en privat IPv6-adress som tilldelats till en befintlig virtuell dator. Du kan inte lägga till en privat IPv6-adress till en IP-konfiguration för alla nätverksgränssnitt som är kopplat till en virtuell dator med verktyg från någon (portal, CLI eller PowerShell).

Du kan inte tilldela en offentlig IPv6-adress till en primär eller sekundär IP-konfigurationen.

## <a name="skus"></a>SKU:er

En offentlig IP-adress har skapats med basic eller standard-SKU. Mer information om skillnaderna mellan SKU finns i [hantera offentliga IP-adresser](virtual-network-public-ip-address.md).

> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar för att skapa en virtuell dator med olika IP-konfigurationer:

|Aktivitet|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverksgränssnitt|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enda NIC-VM med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enda NIC-VM med en privat IPv6-adress (bakom en belastningsutjämnare för Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
