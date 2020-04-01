---
title: Konfigurera IP-adresser för ett Azure-nätverksgränssnitt | Microsoft-dokument
description: Lär dig hur du lägger till, ändrar och tar bort privata och offentliga IP-adresser för ett nätverksgränssnitt.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: b5655a58c3538ac47e8649619b079dc46ee01242
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473229"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Lägga till, ändra eller ta bort IP-adresser för ett Azure-nätverksgränssnitt

Lär dig hur du lägger till, ändrar och tar bort offentliga och privata IP-adresser för ett nätverksgränssnitt. Privata IP-adresser som tilldelats ett nätverksgränssnitt gör det möjligt för en virtuell dator att kommunicera med andra resurser i ett virtuellt Azure-nätverk och anslutna nätverk. En privat IP-adress möjliggör också utgående kommunikation till Internet med hjälp av en oförutsägbar IP-adress. En [offentlig IP-adress](virtual-network-public-ip-address.md) som tilldelats ett nätverksgränssnitt möjliggör inkommande kommunikation till en virtuell dator från Internet. Adressen möjliggör också utgående kommunikation från den virtuella datorn till Internet med hjälp av en förutsägbar IP-adress. Mer information finns [i Förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Om du behöver skapa, ändra eller ta bort ett nätverksgränssnitt läser du artikeln [Hantera ett nätverksgränssnitt.](virtual-network-network-interface.md) Om du behöver lägga till nätverksgränssnitt i eller ta bort nätverksgränssnitt från en virtuell dator läser du artikeln [Lägg till eller ta bort nätverksgränssnitt.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Slutför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto registrerar du dig för ett [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder https://portal.azure.comportalen öppnar och loggar du in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.

Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [behörigheter för nätverksgränssnitt](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Lägga till IP-adresser

Du kan lägga till så många [privata](#private) och [offentliga](#public) [IPv4-adresser](#ipv4) som behövs i ett nätverksgränssnitt, inom de gränser som anges i artikeln [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Du kan lägga till en privat IPv6-adress i en [sekundär IP-konfiguration](#secondary) (så länge det inte finns några befintliga sekundära IP-konfigurationer) för ett befintligt nätverksgränssnitt. Varje nätverksgränssnitt kan ha högst en privat IPv6-adress. Du kan också lägga till en offentlig IPv6-adress i en IPv6-nätverksgränssnittskonfiguration. Mer information om hur du använder IPv6-adresser finns i [IPv6-adresser.](#ipv6)

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj det nätverksgränssnitt som du vill lägga till en IPv4-adress för i listan.
3. Under **INSTÄLLNINGAR**väljer du **IP-konfigurationer**.
4. Under **IP-konfigurationer**väljer du **+ Lägg till**.
5. Ange följande och välj sedan **OK:**

   |Inställning|Krävs?|Information|
   |---|---|---|
   |Namn|Ja|Måste vara unikt för nätverksgränssnittet|
   |Typ|Ja|Eftersom du lägger till en IP-konfiguration i ett befintligt nätverksgränssnitt och varje nätverksgränssnitt måste ha en [primär](#primary) IP-konfiguration, är det enda alternativet **Sekundärt**.|
   |Metod för tilldelning av privat IP-adress|Ja|[**Dynamisk**](#dynamic): Azure tilldelar nästa tillgängliga adress för undernätsadressintervallet som nätverksgränssnittet distribueras i. [**Statisk:**](#static)Du tilldelar en oanvänd adress för det undernätsadressintervall som nätverksgränssnittet distribueras i.|
   |Offentlig IP-adress|Inga|**Inaktiverad:** Ingen offentlig IP-adressresurs är för närvarande kopplad till IP-konfigurationen. **Aktiverad:** Välj en befintlig IPv4 Public IP-adress eller skapa en ny. Om du vill veta hur du skapar en offentlig IP-adress läser du artikeln [Offentliga IP-adresser.](virtual-network-public-ip-address.md#create-a-public-ip-address)|
6. Lägg manuellt till sekundära privata IP-adresser i operativsystemet för virtuella datorer genom att fylla i instruktionerna i artikeln [Tilldela flera IP-adresser till](virtual-network-multiple-ip-addresses-portal.md#os-config) operativsystem för virtuella datorer. Se [privata](#private) IP-adresser för särskilda överväganden innan du manuellt lägger till IP-adresser i ett operativsystem för virtuella datorer. Lägg inte till några offentliga IP-adresser till operativsystemet för virtuella datorer.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Tillägg-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Ändra inställningar för IP-adress

Du kan behöva ändra tilldelningsmetoden för en IPv4-adress, ändra den statiska IPv4-adressen eller ändra den offentliga IP-adressen som tilldelats ett nätverksgränssnitt. Om du ändrar den privata IPv4-adressen för en sekundär IP-konfiguration som är associerad med ett sekundärt nätverksgränssnitt på en virtuell dator (läs mer om [primära och sekundära nätverksgränssnitt) placerar](virtual-network-network-interface-vm.md)du den virtuella datorn i tillståndet stoppad (avtalad) innan du slutför följande steg:

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj det nätverksgränssnitt som du vill visa eller ändra IP-adressinställningar för i listan.
3. Under **INSTÄLLNINGAR**väljer du **IP-konfigurationer**.
4. Välj den IP-konfiguration som du vill ändra i listan.
5. Ändra inställningarna, efter behov, med hjälp av informationen om inställningarna i steg 5 [i Lägg till en IP-konfiguration](#add-ip-addresses).
6. Välj **Spara**.

>[!NOTE]
>Om det primära nätverksgränssnittet har flera IP-konfigurationer och du ändrar den privata IP-adressen för den primära IP-konfigurationen måste du manuellt tilldela om de primära och sekundära IP-adresserna till nätverksgränssnittet i Windows (krävs inte för Linux). Information om hur du manuellt tilldelar IP-adresser till ett nätverksgränssnitt i ett operativsystem finns [i Tilldela flera IP-adresser till virtuella datorer](virtual-network-multiple-ip-addresses-portal.md#os-config). Särskilda överväganden innan du lägger till IP-adresser manuellt i ett operativsystem för virtuella datorer finns i [privata](#private) IP-adresser. Lägg inte till några offentliga IP-adresser till operativsystemet för virtuella datorer.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic ip-config uppdatering](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Ta bort IP-adresser

Du kan ta bort [privata](#private) och [offentliga](#public) IP-adresser från ett nätverksgränssnitt, men ett nätverksgränssnitt måste alltid ha minst en privat IPv4-adress tilldelad.

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Markera det nätverksgränssnitt som du vill ta bort IP-adresser från listan.
3. Under **INSTÄLLNINGAR**väljer du **IP-konfigurationer**.
4. Högerklicka på en [sekundär](#secondary) IP-konfiguration (du kan inte ta bort den [primära](#primary) konfigurationen) som du vill ta bort, välj **Ta bort**och välj sedan **Ja**, för att bekräfta borttagningen. Om konfigurationen hade en offentlig IP-adressresurs kopplad till sig, separeras resursen från IP-konfigurationen, men resursen tas inte bort.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic ip-config bort](/cli/azure/network/nic/ip-config)|
|PowerShell|[Ta bort-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-konfigurationer

[Privata](#private) och (valfritt) [offentliga](#public) IP-adresser tilldelas en eller flera IP-konfigurationer som tilldelats ett nätverksgränssnitt. Det finns två typer av IP-konfigurationer:

### <a name="primary"></a>Primär

Varje nätverksgränssnitt tilldelas en primär IP-konfiguration. En primär IP-konfiguration:

- Har en [privat](#private) [IPv4-adress](#ipv4) tilldelad. Du kan inte tilldela en privat [IPv6-adress](#ipv6) till en primär IP-konfiguration.
- Kan också ha en [offentlig](#public) IPv4-adress tilldelad. Du kan inte tilldela en offentlig IPv6-adress till en primär (IPv4) IP-konfiguration. 

### <a name="secondary"></a>Sekundär

Förutom en primär IP-konfiguration kan ett nätverksgränssnitt ha noll eller fler sekundära IP-konfigurationer tilldelade till det. En sekundär IP-konfiguration:

- Måste ha tilldelats en privat IPv4- eller IPv6-adress. Om adressen är IPv6 kan nätverksgränssnittet bara ha en sekundär IP-konfiguration. Om adressen är IPv4 kan nätverksgränssnittet ha flera sekundära IP-konfigurationer tilldelade. Mer information om hur många privata och offentliga IPv4-adresser som kan tilldelas ett nätverksgränssnitt finns i artikeln [Azure-begränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Kan också ha tilldelats en offentlig IPv4- eller IPv6-adress. Det är praktiskt att tilldela flera IPv4-adresser till ett nätverksgränssnitt i scenarier som:
  - Värd för flera webbplatser eller tjänster med olika IP-adresser och TLS/SSL-certifikat på en enda server.
  - En virtuell dator som fungerar som en virtuell nätverksinstallation, till exempel en brandvägg eller belastningsutjämnare.
  - Möjligheten att lägga till någon av de privata IPv4-adresserna för något av nätverksgränssnitten i en Azure Load Balancer-backend-pool. Tidigare kunde endast den primära IPv4-adressen för det primära nätverksgränssnittet läggas till i en backend-pool. Mer information om hur du läser in flera IPv4-konfigurationer finns i artikeln [Belastningsutjämning av flera IP-konfigurationer.](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
  - Möjligheten att läsa in en IPv6-adress som tilldelats ett nätverksgränssnitt. Mer information om hur du läser in aldot till en privat IPv6-adress finns i artikeln [Belastningsutjämning iPv6-adresser.](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="address-types"></a>Adresstyper

Du kan tilldela följande typer av IP-adresser till en [IP-konfiguration:](#ip-configurations)

### <a name="private"></a>Privat

Privata [IPv4-](#ipv4) eller IPv6-adresser gör det möjligt för en virtuell dator att kommunicera med andra resurser i ett virtuellt nätverk eller andra anslutna nätverk. 

Som standard tilldelar Azure DHCP-servrar den privata IPv4-adressen för den [primära IP-konfigurationen](#primary) av Azure-nätverksgränssnittet till nätverksgränssnittet i operativsystemet för virtuella datorer. Om det inte behövs bör du aldrig manuellt ange IP-adressen för ett nätverksgränssnitt i den virtuella datorns operativsystem.

> [!WARNING]
> Om IPv4-adressen som angetts som den primära IP-adressen för ett nätverksgränssnitt i en virtuell dators operativsystem någonsin skiljer sig från den privata IPv4-adress som tilldelats den primära IP-konfigurationen för det primära nätverksgränssnittet som är kopplat till en virtuell dator i Azure, förlorar du anslutningen till den virtuella datorn.

Det finns scenarier där det är nödvändigt att manuellt ange IP-adressen för ett nätverksgränssnitt i den virtuella datorns operativsystem. Du måste till exempel manuellt ange de primära och sekundära IP-adresserna för ett Windows-operativsystem när du lägger till flera IP-adresser till en virtuell Azure-dator. För en virtuell Linux-dator behöver du bara manuellt ange de sekundära IP-adresserna. Mer information finns [i Lägga till IP-adresser i ett vm-operativsystem.](virtual-network-multiple-ip-addresses-portal.md#os-config) Om du behöver ändra adressen som tilldelats en IP-konfiguration rekommenderar vi att du:

1. Kontrollera att den virtuella datorn tar emot en adress från Azure DHCP-servrarna. När du har gjort det ändrar du tillbaka tilldelningen av IP-adressen till DHCP i operativsystemet och startar om den virtuella datorn.
2. Stoppa (deallocate) den virtuella datorn.
3. Ändra IP-adressen för IP-konfigurationen i Azure.
4. Starta den virtuella datorn.
5. [Konfigurera](virtual-network-multiple-ip-addresses-portal.md#os-config) de sekundära IP-adresserna manuellt i operativsystemet (och även den primära IP-adressen i Windows) så att de matchar det du anger i Azure.

Genom att följa föregående steg förblir den privata IP-adressen som tilldelats nätverksgränssnittet i Azure och inom en virtuell dators operativsystem densamma. Om du vill hålla reda på vilka virtuella datorer i din prenumeration som du manuellt har angett IP-adresser i ett operativsystem för kan du överväga att lägga till en [Azure-tagg](../azure-resource-manager/management/tag-resources.md) till de virtuella datorerna. Du kan till exempel använda "IP-adresstilldelning: Statisk". På så sätt kan du enkelt hitta de virtuella datorerna i din prenumeration som du manuellt har angett IP-adressen för i operativsystemet.

Förutom att göra det möjligt för en virtuell dator att kommunicera med andra resurser inom samma, eller anslutna virtuella nätverk, gör en privat IP-adress också det möjligt för en virtuell dator att kommunicera utgående till Internet. Utgående anslutningar är källnätverksadress som översätts av Azure till en oförutsägbar offentlig IP-adress. Mer information om Azure-utgående Internet-anslutning läser du artikeln [för utgående Internet-anslutning från Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Du kan inte kommunicera inkommande till en virtuell dators privata IP-adress från Internet. Om dina utgående anslutningar kräver en förutsägbar offentlig IP-adress associerar du en offentlig IP-adressresurs till ett nätverksgränssnitt.

### <a name="public"></a>Offentlig

Offentliga IP-adresser som tilldelas via en offentlig IP-adressresurs möjliggör inkommande anslutning till en virtuell dator från Internet. Utgående anslutningar till Internet använder en förutsägbar IP-adress. Mer information [finns i Hur du na ser Hur du kan se utgående anslutningar i Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Du kan tilldela en offentlig IP-adress till en IP-konfiguration, men behöver inte. Om du inte tilldelar en offentlig IP-adress till en virtuell dator genom att associera en offentlig IP-adressresurs kan den virtuella datorn fortfarande kommunicera utgående till Internet. I det här fallet är den privata IP-adressen källnätverksadressen översatt av Azure till en oförutsägbar offentlig IP-adress. Mer information om offentliga IP-adressresurser finns i [Offentlig IP-adressresurs](virtual-network-public-ip-address.md).

Det finns gränser för antalet privata och offentliga IP-adresser som du kan tilldela ett nätverksgränssnitt. Mer information finns i artikeln [Azure limits.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

> [!NOTE]
> Azure översätter en virtuell dators privata IP-adress till en offentlig IP-adress. Därför är en virtuell dators operativsystem omedveten om någon offentlig IP-adress som tilldelats det, så det finns ingen anledning att någonsin manuellt tilldela en offentlig IP-adress i operativsystemet.

## <a name="assignment-methods"></a>Tilldelningsmetoder

Offentliga och privata IP-adresser tilldelas med någon av följande tilldelningsmetoder:

### <a name="dynamic"></a>Dynamisk

Dynamiska privata IPv4- och IPv6-adresser (valfritt) tilldelas som standard.

- **Endast offentlig:** Azure tilldelar adressen från ett intervall som är unikt för varje Azure-region. Information om vilka områden som tilldelas till varje region finns i [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Adressen kan ändras när en virtuell dator stoppas (frigörs) och sedan startas igen. Du kan inte tilldela en offentlig IPv6-adress till en IP-konfiguration med hjälp av någon av tilldelningsmetoderna.
- **Endast privat**: Azure reserverar de första fyra adresserna i varje undernätsadressintervall och tilldelar inte adresserna. Azure tilldelar nästa tillgängliga adress till en resurs från adressintervallet i undernätet. Om till exempel undernätets adressintervall är 10.0.0.0/16, och adresserna 10.0.0.0.4-10.0.0.14 redan har tilldelats (.0-.3 är reserverade) tilldelar Azure 10.0.0.15 till resursen. Dynamisk är standardinställningen för allokering. När de har tilldelats släpps endast dynamiska IP-adresser om ett nätverksgränssnitt har tagits bort, tilldelats ett annat undernät inom samma virtuella nätverk eller om allokeringsmetoden ändras till statisk och en annan IP-adress anges. Som standard tilldelar Azure den tidigare dynamiskt tilldelade adressen som statisk adress när du ändrar allokeringsmetod från dynamisk till statisk. 

### <a name="static"></a>Statisk

Du kan (alternativt) tilldela en offentlig eller privat statisk IPv4- eller IPv6-adress till en IP-konfiguration. Mer information om hur Azure tilldelar statiska offentliga IPv4-adresser finns i [Offentliga IP-adresser](virtual-network-public-ip-address.md).

- **Endast offentlig:** Azure tilldelar adressen från ett intervall som är unikt för varje Azure-region. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064). Adressen ändras inte förrän den offentliga IP-adressresurs som den har tilldelats tas bort eller tilldelningsmetoden ändras till dynamisk. Om den offentliga IP-adressresursen är kopplad till en IP-konfiguration måste den skiljas från IP-konfigurationen innan tilldelningsmetoden ändras.
- **Endast privat**: Du väljer och tilldelar en adress från undernätets adressintervall. Adressen som du tilldelar kan vara alla adresser i adressintervallet i undernätet som inte är någon av de fyra första adresserna i undernätets adressintervall och som inte är tilldelade till någon annan resurs i undernätet. Statiska adresser släpps endast om ett nätverksgränssnitt tas bort. Om du ändrar allokeringsmetoden till statisk, tilldelar Azure dynamiskt den tidigare tilldelade dynamiska IP-adressen som statisk adress, även om adressen inte är nästa tillgängliga adress i undernätets adressintervall. Adressen ändras också om nätverksgränssnittet har tilldelats ett annat undernät i samma virtuella nätverk. För att tilldela nätverksgränssnittet till ett annat undernät måste du emellertid först ändra allokeringsmetoden från statisk till dynamisk. När nätverksgränssnittet har tilldelats ett annat undernät kan du ändra allokeringsmetod till statisk och tilldela en IP-adress från det nya undernätets adressintervall.

## <a name="ip-address-versions"></a>IP-adressversioner

Du kan ange följande versioner när du tilldelar adresser:

### <a name="ipv4"></a>IPv4

Varje nätverksgränssnitt måste ha en [primär](#primary) IP-konfiguration med en tilldelad [privat](#private) [IPv4-adress.](#ipv4) Du kan lägga till en eller flera [sekundära](#secondary) IP-konfigurationer som var och en har en privat IPv4-adress och (eventuellt) en offentlig IPv4-IP-adress. [public](#public)

### <a name="ipv6"></a>IPv6

Du kan tilldela noll eller en privat [IPv6-adress](#ipv6) till en sekundär IP-konfiguration för ett nätverksgränssnitt. Nätverksgränssnittet kan inte ha några befintliga sekundära IP-konfigurationer. Varje nätverksgränssnitt kan ha högst en privat IPv6-adress. Du kan också lägga till en offentlig IPv6-adress i en IPv6-nätverksgränssnittskonfiguration.

> [!NOTE]
> Även om du kan skapa ett nätverksgränssnitt med en IPv6-adress med portalen kan du inte lägga till ett befintligt nätverksgränssnitt i en ny eller befintlig virtuell dator med hjälp av portalen. Använd PowerShell eller Azure CLI för att skapa ett nätverksgränssnitt med en privat IPv6-adress och bifoga sedan nätverksgränssnittet när du skapar en virtuell dator. Du kan inte koppla ett nätverksgränssnitt med en privat IPv6-adress som tilldelats det till en befintlig virtuell dator. Du kan inte lägga till en privat IPv6-adress i en IP-konfiguration för alla nätverksgränssnitt som är anslutna till en virtuell dator med verktyg (portal, CLI eller PowerShell).

Du kan inte tilldela en offentlig IPv6-adress till en primär eller sekundär IP-konfiguration.

## <a name="skus"></a>SKU: er

En offentlig IP-adress skapas med den grundläggande SKU eller standard-SKU. Mer information om SKU-skillnader finns i [Hantera offentliga IP-adresser](virtual-network-public-ip-address.md).

> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="next-steps"></a>Nästa steg
Så här skapar du en virtuell dator med olika IP-konfigurationer:

|Aktivitet|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverksgränssnitt|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en virtuell nätverkskort med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en virtuell dator med ett NIC-nätverkskort med en privat IPv6-adress (bakom en Azure Load Balancer)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
