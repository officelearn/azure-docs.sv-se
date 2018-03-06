---
title: "Konfigurera IP-adresser för en Azure nätverksgränssnittet | Microsoft Docs"
description: "Lär dig mer om att lägga till, ändra och ta bort privata och offentliga IP-adresser för ett nätverksgränssnitt."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
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
ms.openlocfilehash: 478a2ebfa6a4cc504119734ac2f67b1f7c77dd5a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Lägga till, ändra eller ta bort IP-adresser för ett Azure-nätverk-gränssnitt

Lär dig mer om att lägga till, ändra och ta bort offentliga och privata IP-adresser för ett nätverksgränssnitt. Privata IP-adresser tilldelas ett nätverksgränssnitt kan en virtuell dator att kommunicera med andra resurser i Azure-nätverk och anslutna nätverk. En privat IP-adress kan också utgående kommunikation till Internet via en oförutsägbart IP-adress. En [offentliga IP-adressen](virtual-network-public-ip-address.md) tilldelas en gränssnittet aktiverar inkommande nätverkskommunikationen till en virtuell dator från Internet. Adressen kan även utgående kommunikation från den virtuella datorn till Internet via en förutsägbar IP-adress. Mer information finns i [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Om du behöver för att skapa, ändra eller ta bort ett nätverksgränssnitt, kan du läsa den [hantera ett nätverksgränssnitt](virtual-network-network-interface.md) artikel. Om du behöver lägga till nätverksgränssnitt som eller ta bort nätverksgränssnitt från en virtuell dator kan läsa den [Lägg till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md) artikel. 


## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.2.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.26 eller senare. Kör `az --version` att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

## <a name="add-ip-addresses"></a>Lägg till IP-adresser

Du kan lägga till så många [privata](#private) och [offentliga](#public) [IPv4](#ipv4) adresser som behövs för att ett nätverksgränssnitt inom de gränser som anges i den [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikel. Du kan inte använda portalen för att lägga till en IPv6-adress till ett befintligt nätverksgränssnitt (även om du kan använda portalen för att lägga till en privat IPv6-adress till ett nätverksgränssnitt när du skapar nätverksgränssnittet). Du kan använda PowerShell eller CLI för att lägga till en privat IPv6-adress i en [sekundära IP-konfiguration](#secondary) (så länge det finns inga befintliga sekundär IP-konfigurationer) för en befintlig nätverksgränssnitt som inte är kopplad till en virtuell dator. Du kan inte använda ett verktyg för att lägga till en offentlig IPv6-adress till ett nätverksgränssnitt. Se [IPv6](#ipv6) mer information om hur du använder IPv6-adresser. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på nätverksgränssnittet som du vill lägga till en IPv4-adress för.
4. Klicka på **IP-konfigurationer** i den **inställningar** på bladet för nätverksgränssnittet som du har valt.
5. Klicka på **+ Lägg till** i bladet som öppnas för IP-konfigurationer.
6. Ange följande och klicka sedan på **OK** att stänga den **lägga till IP-konfiguration** bladet:

    |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Måste vara unikt för nätverksgränssnittet|
    |Typ|Ja|Eftersom du vill lägga till en IP-konfiguration till en befintlig nätverksgränssnitt, och varje nätverksgränssnitt måste ha en [primära](#primary) IP-konfiguration, det enda alternativet är **sekundära**.|
    |Tilldelningsmetod för privat IP-adress|Ja|[**Dynamiska**](#dynamic): Azure tilldelar nästa tillgängliga adress nätverksgränssnittet har distribuerats i undernätet-adressintervallet. [**Statisk**](#static): du tilldelar en oanvända adress nätverksgränssnittet har distribuerats i undernätet-adressintervallet.|
    |Offentlig IP-adress|Nej|**Inaktiverat:** ingen offentlig IP-adressresurs är för närvarande associerad till IP-konfigurationen. **Aktiverad:** Välj en befintlig offentlig IPv4-IP-adress eller skapa en ny. Om du vill veta hur du skapar en offentlig IP-adress, läsa den [offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address) artikel.|
7. Manuellt lägga till sekundära privata IP-adresser till virtuella operativsystem genom att slutföra anvisningarna i den [tilldela flera IP-adresser till virtuella operativsystem](virtual-network-multiple-ip-addresses-portal.md#os-config) artikel. Se [privata](#private) IP-adresser för speciella överväganden innan du lägger till IP-adresser till ett operativsystem för virtuell dator manuellt. Lägg inte till några offentliga IP-adresser i det virtuella operativsystemet.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[Skapa AZ nätverket nic ip-konfiguration](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>Ändra inställningar för IP-adress

Du kan måste ändra tilldelningsmetod av IPv4-adress, ändra statiska IPv4-adress, eller ändra den offentliga IP-adress som tilldelats ett nätverksgränssnitt. Om du ändrar privata IPv4-adressen för en sekundär IP-konfiguration som är kopplad till ett sekundärt nätverksgränssnitt i en virtuell dator (Läs mer om [primära och sekundära nätverksgränssnitt](virtual-network-network-interface-vm.md)), placera den virtuella datorn i tillståndet Stoppad (frigjord) innan du slutför följande steg: 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klickar du på nätverksgränssnittet som du vill visa eller ändra inställningar för IP-adresser.
4. Klicka på **IP-konfigurationer** i den **inställningar** på bladet för nätverksgränssnittet som du har valt.
5. Klicka på IP-konfiguration som du vill ändra i listan i bladet som öppnas för IP-konfigurationer.
6. Ändra inställningarna efter behov, med information om inställningarna i steg 6 i den [lägga till en IP-konfiguration](#create-ip-config) i den här artikeln. Klicka på **spara** att stänga bladet för IP-konfiguration som du har ändrat.

>[!NOTE]
>Om det primära nätverksgränssnittet har flera IP-konfigurationer och ändrar privata IP-adressen för primär IP-konfigurationen, måste du manuellt tilldela de primära och sekundära IP-adresserna till nätverksgränssnittet i Windows (krävs inte för Linux). Manuellt tilldela IP-adresser till ett nätverksgränssnitt inom ett operativsystem genom att läsa den [tilldela flera IP-adresser till virtuella datorer](virtual-network-multiple-ip-addresses-portal.md#os-config) artikel. Se [privata](#private) IP-adresser för speciella överväganden innan du lägger till IP-adresser till ett operativsystem för virtuell dator manuellt. Lägg inte till några offentliga IP-adresser i det virtuella operativsystemet.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ nätverket nic ip-config uppdatering](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Ta bort IP-adresser

Du kan ta bort [privata](#private) och [offentliga](#public) IP-adresser från ett nätverksgränssnitt, men ett nätverksgränssnitt måste alltid ha minst en privat IPv4-adress tilldelas.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Läs den [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artikeln om du vill veta mer om hur du tilldelar roller och behörigheter till konton.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *nätverksgränssnitt*. När **nätverksgränssnitt** visas i sökresultaten klickar du på den.
3. I den **nätverksgränssnitt** bladet som visas, klicka på nätverksgränssnittet som du vill ta bort IP-adresser från.
4. Klicka på **IP-konfigurationer** i den **inställningar** på bladet för nätverksgränssnittet som du har valt.
5. Högerklicka på en [sekundära](#secondary) IP-konfiguration (du kan inte ta bort den [primära](#primary) configuration) du vill ta bort, klicka på **ta bort**, klicka sedan på **Ja** att bekräfta borttagningen. Om konfigurationen har en offentlig IP-adressresurs som är kopplade till den, resursen har kopplats bort från IP-konfigurationen, men resursen tas inte bort.
6. Stäng den **IP-konfigurationer** bladet.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[ta bort AZ nätverket nic ip-konfiguration](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>IP-konfigurationer

[Privata](#private) och (valfritt) [offentliga](#public) IP-adresser tilldelas till en eller flera IP-konfigurationer som tilldelats ett nätverksgränssnitt. Det finns två typer av IP-konfigurationer:

### <a name="primary"></a>Primär

Varje nätverksgränssnitt tilldelas en primär IP-konfiguration. En primär IP-konfiguration:

- Har en [privata](#private) [IPv4](#ipv4) adress som tilldelats till den. Du kan inte tilldela en privat [IPv6](#ipv6) adressen till en primär IP-konfiguration.
- Kan också ha en [offentliga](#public) IPv4-adress som tilldelats till den. Du kan inte tilldela en offentlig IPv6-adress till en primär eller sekundär IP-konfiguration. Däremot kan du tilldela en offentlig IPv6-adress till en Azure belastningsutjämnare som kan läsa in Utjämna trafiken till en virtuell dators privat IPv6-adress. Mer information finns i [information och begränsningar för IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Sekundär

Förutom en primär IP-konfiguration, kan ett nätverksgränssnitt ha noll eller flera sekundära IP-konfigurationer som tilldelats. En sekundär IP-konfiguration:

- Ha måste en privat IPv4- eller IPv6-adress tilldelas till den. Om adressen är IPv6, kan nätverksgränssnittet bara ha en sekundär IP-konfiguration. Om adressen är IPv4, kan nätverkskortet ha flera sekundära IP-konfigurationer som tilldelats. Mer information om hur många privata och offentliga IPv4-adresser kan tilldelas till ett nätverksgränssnitt finns i [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikel.  
- Också kan har en offentlig IPv4-adress kopplad till den, om den privata IP-adressen är IPv4. Om den privata IP-adressen är IPv6, kan du tilldela en offentlig IPv4- eller IPv6-adress till IP-konfigurationen. Tilldela flera IP-adresser till ett nätverksgränssnitt är användbart i scenarier som:
    - Du kan hantera flera webbplatser eller tjänster med olika IP-adresser och SSL-certifikat på en enda server.
    - En virtuell dator som fungerar som en virtuell nätverksenhet, till exempel en brandvägg eller läsa in belastningsutjämning.
    - Möjligheten att lägga till någon av de privata IPv4-adresserna för nätverksgränssnitten i en Azure-belastningsutjämnaren backend-adresspool. Tidigare kunde endast primära IPv4-adressen för det primära nätverksgränssnittet läggas till en backend-adresspool. Läs mer om hur du belastningsutjämna flera IPv4-konfigurationer i den [flera IP-konfigurationer för belastningsutjämning](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel. 
    - Möjligheten att läsa in balansera en IPv6-adress som tilldelats ett nätverksgränssnitt. Mer information om hur du belastningsutjämna till en privat IPv6-adress finns på [belastningsutjämna IPv6-adresser](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel.


## <a name="address-types"></a>Adresstyper

Du kan tilldela följande typer av IP-adresser till ett [IP-konfiguration](#ip-configurations):

### <a name="private"></a>Privat

Privata [IPv4](#ipv4) adresser aktivera en virtuell dator att kommunicera med andra resurser i ett virtuellt nätverk eller andra anslutna nätverk. En virtuell dator kan inte lämnas inkommande eller kan den virtuella datorn kommunicera utgående med en privat [IPv6](#ipv6) adress med ett undantag. En virtuell dator kan kommunicera med Azure belastningsutjämnaren med hjälp av en IPv6-adress. Mer information finns i [information och begränsningar för IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Som standard tilldelar Azure DHCP-servrar privat IPv4-adress för den [primära IP-konfigurationen](#primary) för Azure nätverksgränssnittet till nätverksgränssnitt i virtuella operativsystem. Om nödvändigt, ska du aldrig manuellt ange IP-adressen för ett nätverksgränssnitt på den virtuella datorns operativsystem. 

> [!WARNING]
> Om IPv4-adressen som angetts som primär IP-adressen för ett nätverksgränssnitt i en virtuell dators operativsystem är allt annat än privat IPv4-adress som tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet som är kopplad till en virtuell dator i Azure, förlora anslutningen till den virtuella datorn.

Det finns scenarier där det är nödvändigt att ange IP-adressen för ett nätverksgränssnitt på den virtuella datorns operativsystem manuellt. Du måste till exempel manuellt ange primära och sekundära IP-adresser för en Windows-operativsystemet när du lägger till flera IP-adresser till en virtuell Azure-dator. För en virtuell Linux-dator, kan behöver du bara ange manuellt sekundära IP-adresser. Se [lägga till IP-adresser till ett VM-operativsystem](virtual-network-multiple-ip-addresses-portal.md#os-config) mer information. Om du behöver ändra den adress som tilldelats en IP-konfiguration, rekommenderar vi som du:

1. Se till att den virtuella datorn tar emot en adress från Azure DHCP-servrar. När du har kan ändra tilldelningen av IP-adressen till DHCP i operativsystemet och starta om den virtuella datorn.
2. Stoppa (frigöra) den virtuella datorn.
3. Ändra IP-adress för IP-konfiguration i Azure.
4. Startar den virtuella datorn.
5. [Konfigurera manuellt](virtual-network-multiple-ip-addresses-portal.md#os-config) sekundära IP-adresser inom operativsystemet (och även den primära IP-adressen i Windows) så att den matchar vad du anger i Azure.
 
Genom att följa desamma de här stegen, privat IP-adress som tilldelats nätverksgränssnittet i Azure, och inom en virtuell dators operativsystem. Om du vill hålla reda på vilka virtuella datorer i din prenumeration som du har konfigurerat IP-adresser inom ett operativsystem för manuellt, Överväg att lägga till en Azure [taggen](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) till de virtuella datorerna. Du kan använda ”IP-adresstilldelning: statisk”, till exempel. På så sätt kan du lätt kan hitta de virtuella datorerna i din prenumeration som du har konfigurerat IP-adress för operativsystemet manuellt.

Förutom att aktivera en virtuell dator att kommunicera med andra resurser i samma eller anslutna virtuella nätverk, kan en privat IP-adress även en virtuell dator att kommunicera utgående till Internet. Utgående anslutningar är källan nätverksadress översättas av Azure en oförutsägbart offentliga IP-adress. Om du vill veta mer om Azure utgående Internetanslutning kan du läsa den [Azure utgående Internetanslutning](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel. Du kan inte kommunicera inkommande från Internet till en virtuell dators privata IP-adress. Om din utgående anslutningar kräver en förutsägbar offentliga IP-adress, associera en offentlig IP-adressresurs till ett nätverksgränssnitt.

### <a name="public"></a>Offentligt

Offentliga IP-adresser som tilldelats via en offentlig IP-adressresurs tillåta inkommande anslutningar till en virtuell dator från Internet. Utgående anslutningar till Internet använder en förutsägbar IP-adress. Se [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mer information. Du kan tilldela en offentlig IP-adress till en IP-konfiguration, men inte behöver. Om du inte tilldelar en offentlig IP-adress till en virtuell dator genom att associera en offentlig IP-adressresurs, kan den virtuella datorn fortfarande kommunicera utgående till Internet. I det här fallet är den privata IP-adressen adress källnätverket översättas av Azure en oförutsägbart offentliga IP-adress. Mer information om offentlig IP-adressresurser finns [offentliga IP-adressresurs](virtual-network-public-ip-address.md).

Det finns begränsningar för antalet privata och offentliga IP-adresser som du kan tilldela ett nätverksgränssnitt. Mer information finns i [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artikel.

> [!NOTE]
> Azure omvandlar en virtuell dators privata IP-adressen till en offentlig IP-adress. En virtuell dators operativsystem är därför ovetande om eventuella offentlig IP-adress som tilldelats, så det ingen behövs någonsin manuellt tilldela en offentlig IP-adress i operativsystemet.

## <a name="assignment-methods"></a>Metoder

Offentliga och privata IP-adresser tilldelas genom att använda någon av följande tilldelningsmetoder:

### <a name="dynamic"></a>Dynamisk

Dynamisk privata IPv4 och IPv6 tilldelas adresser (valfritt) som standard. 

- **Endast allmän**: Azure tilldelas adress från ett intervall som är unik för varje Azure-region. Information om vilka adressintervall som är kopplade till varje region finns [IP-intervall för Microsoft Azure-Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Adressen kan ändras när en virtuell dator har stoppats (frigjorts), sedan startas igen. Du kan inte tilldela en IP-konfiguration med hjälp av antingen tilldelningsmetod en offentlig IPv6-adress.
- **Endast privata**: Azure reserverar de fyra första adresserna i varje undernät-adressintervall och adresserna inte tilldelas. Azure tilldelar nästa tillgängliga adress till en resurs från adressintervallet i undernätet. Om till exempel undernätets adressintervall är 10.0.0.0/16, och adresserna 10.0.0.0.4-10.0.0.14 redan har tilldelats (.0-.3 är reserverade) tilldelar Azure 10.0.0.15 till resursen. Dynamisk är standardinställningen för allokering. När de har tilldelats släpps endast dynamiska IP-adresser om ett nätverksgränssnitt har tagits bort, tilldelats ett annat undernät inom samma virtuella nätverk eller om allokeringsmetoden ändras till statisk och en annan IP-adress anges. Som standard tilldelar Azure den tidigare dynamiskt tilldelade adressen som statisk adress när du ändrar allokeringsmetod från dynamisk till statisk. Du kan bara tilldela en privat IPv6-adress med metoden dynamisk tilldelning.

### <a name="static"></a>Statisk

Du kan tilldela en offentlig eller privat statiska IPv4-adress (valfritt) till en IP-konfiguration. Du kan inte tilldela en statisk offentlig eller privat IPv6-adress till en IP-konfiguration. Mer information om hur Azure tilldelar statiska IPv4-adresser som finns i [offentliga IP-adressen](virtual-network-public-ip-address.md) artikel.

- **Endast allmän**: Azure tilldelas adress från ett intervall som är unik för varje Azure-region. Information om vilka adressintervall som är kopplade till varje region finns [IP-intervall för Microsoft Azure-Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Adressen ändras inte förrän den offentliga IP-adressresurs tilldelas tas bort eller tilldelning metoden ändras till dynamisk. Om den offentliga IP-adressresursen är kopplad till en IP-konfiguration måste tas bort från IP-konfiguration innan du ändrar dess tilldelningsmetod.
- **Endast privata**: du väljer och tilldela en adress från undernätets adressintervall. Adressen som du tilldelar kan vara alla adresser i adressintervallet i undernätet som inte är någon av de fyra första adresserna i undernätets adressintervall och som inte är tilldelade till någon annan resurs i undernätet. Statiska adresser släpps endast om ett nätverksgränssnitt tas bort. Om du ändrar allokeringsmetoden till statisk tilldelar Azure dynamiskt de tidigare tilldelade statiska IP-adresserna som dynamiska adresser, även om adressen inte är nästa tillgängliga adress i undernätets adressintervall. Adressen ändras också om nätverksgränssnittet har tilldelats ett annat undernät i samma virtuella nätverk. För att tilldela nätverksgränssnittet till ett annat undernät måste du emellertid först ändra allokeringsmetoden från statisk till dynamisk. När nätverksgränssnittet har tilldelats ett annat undernät kan du ändra allokeringsmetod till statisk och tilldela en IP-adress från det nya undernätets adressintervall.

## <a name="ip-address-versions"></a>IP-adress versioner

När du tilldelar adresser kan du ange följande versioner:

### <a name="ipv4"></a>IPv4

Varje nätverksgränssnitt måste ha ett [primära](#primary) IP-konfiguration med en tilldelad [privata](#private) [IPv4](#ipv4) adress. Du kan lägga till en eller flera [sekundära](#secondary) IP-konfigurationer som har en privat IPv4 och (valfritt) en IPv4 [offentliga](#public) IP-adress.

### <a name="ipv6"></a>IPv6

Du kan tilldela noll eller ett privat [IPv6](#ipv6) adress till en sekundär IP-konfiguration för ett nätverksgränssnitt. Nätverksgränssnittet kan inte ha några befintliga sekundär IP-konfigurationer. Du kan inte lägga till en IP-konfiguration med en IPv6-adress med hjälp av portalen. Använd PowerShell eller CLI för att lägga till en IP-konfiguration med en privat IPv6-adress till en befintlig nätverksgränssnitt. Nätverksgränssnittet kan inte kopplas till en befintlig virtuell dator.

> [!NOTE]
> Om du kan skapa ett nätverksgränssnitt med en IPv6-adress med hjälp av portalen, du kan inte lägga till en befintlig nätverksgränssnittet till en ny eller befintlig virtuell dator med hjälp av portalen. Använd PowerShell eller Azure CLI 2.0 för att skapa ett nätverksgränssnitt med en privat IPv6-adress och bifogar nätverksgränssnittet när du skapar en virtuell dator. Du kan inte koppla ett nätverksgränssnitt med en privat IPv6-adress som tilldelats till en befintlig virtuell dator. Du kan inte lägga till en privat IPv6-adress till en IP-konfiguration för alla nätverksgränssnitt som är kopplad till en virtuell dator med alla verktyg (portal, CLI eller PowerShell).

Du kan inte tilldela en offentlig IPv6-adress till en primär eller sekundär IP-konfiguration.

## <a name="skus"></a>SKU:er

En offentlig IP-adress har skapats med basic eller standard-SKU.  Mer information om SKU skillnader finns [hantera offentliga IP-adresser](virtual-network-public-ip-address.md).

> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar för att skapa en virtuell dator med olika IP-konfigurationer:

|Aktivitet|Verktyget|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enda NIC VM med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enda NIC VM med en privat IPv6-adress (bakom en belastningsutjämnare i Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
