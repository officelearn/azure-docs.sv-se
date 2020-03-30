---
title: Skapa, ändra eller ta bort ett Azure-nätverksgränssnitt
titlesuffix: Azure Virtual Network
description: Lär dig vad ett nätverksgränssnitt är och hur du skapar, ändrar inställningar för och tar bort ett.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 11e6285ef70ffde5344add951801997f8541eaad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244945"
---
# <a name="create-change-or-delete-a-network-interface"></a>Skapa, ändra eller ta bort ett nätverksgränssnitt

Lär dig hur du skapar, ändrar inställningar för och tar bort ett nätverksgränssnitt. Ett nätverksgränssnitt gör det möjligt för en virtuell Azure-dator att kommunicera med internet-, Azure- och lokala resurser. När du skapar en virtuell dator med Azure-portalen skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Du kan i stället välja att skapa nätverksgränssnitt med anpassade inställningar och lägga till ett eller flera nätverksgränssnitt på en virtuell dator när du skapar den. Du kanske också vill ändra standardinställningarna för nätverksgränssnittet för ett befintligt nätverksgränssnitt. I den här artikeln beskrivs hur du skapar ett nätverksgränssnitt med anpassade inställningar, ändrar befintliga inställningar, till exempel tilldelning av nätverksfilter (nätverkssäkerhetsgrupp), tilldelning av undernät, DNS-serverinställningar och IP-vidarebefordran samt tar bort ett nätverksgränssnitt.

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt läser du [Hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du behöver lägga till nätverksgränssnitt i eller ta bort nätverksgränssnitt från virtuella datorer läser du [Lägga till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Slutför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto registrerar du dig för ett [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder https://portal.azure.comportalen öppnar och loggar du in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.

Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

När du skapar en virtuell dator med Azure-portalen skapar portalen ett nätverksgränssnitt med standardinställningar för dig. Om du hellre vill ange alla inställningar för nätverksgränssnittet kan du skapa ett nätverksgränssnitt med anpassade inställningar och ansluta nätverksgränssnittet till en virtuell dator när du skapar den virtuella datorn (med PowerShell eller Azure CLI). Du kan också skapa ett nätverksgränssnitt och lägga till det på en befintlig virtuell dator (med PowerShell eller Azure CLI). Mer information om hur du skapar en virtuell dator med ett befintligt nätverksgränssnitt eller lägger till eller tar bort nätverksgränssnitt från befintliga virtuella datorer finns [i Lägga till eller ta bort nätverksgränssnitt](virtual-network-network-interface-vm.md). Innan du skapar ett nätverksgränssnitt måste du ha ett befintligt [virtuellt nätverk](manage-virtual-network.md) på samma plats och prenumeration som du skapar ett nätverksgränssnitt i.

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj **+ Lägg till** under **Nätverksgränssnitt**.
3. Ange eller välj värden för följande inställningar och välj sedan **Skapa:**

    |Inställning|Krävs?|Information|
    |---|---|---|
    |Namn|Ja|Namnet måste vara unikt inom den resursgrupp du väljer. Med tiden har du troligen flera nätverksgränssnitt i din Azure-prenumeration. Förslag på hur du skapar en namngivningskonvention för att göra det enklare att hantera flera nätverksgränssnitt finns i [Namngivningskonventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Det går inte att ändra namnet när nätverksgränssnittet har skapats.|
    |Virtuellt nätverk|Ja|Välj det virtuella nätverket för nätverksgränssnittet. Du kan bara tilldela ett nätverksgränssnitt till ett virtuellt nätverk som finns i samma prenumeration och plats som nätverksgränssnittet. När ett nätverksgränssnitt har skapats kan du inte ändra det virtuella nätverk som det har tilldelats. Den virtuella datorn som du lägger till nätverksgränssnittet i måste också finnas på samma plats och prenumeration som nätverksgränssnittet.|
    |Undernät|Ja|Markera ett undernät i det virtuella nätverk som du har valt. Du kan ändra undernätet som nätverksgränssnittet tilldelas när det har skapats.|
    |Tilldelning av privata IP-adresser|Ja| I den här inställningen väljer du tilldelningsmetoden för IPv4-adressen. Välj mellan följande tilldelningsmetoder: **Dynamisk:** När du väljer det här alternativet tilldelar Azure automatiskt nästa tillgängliga adress från adressutrymmet i det undernät du valde. **Statisk:** När du väljer det här alternativet måste du manuellt tilldela en tillgänglig IP-adress från adressutrymmet i det undernät du valde. Statiska och dynamiska adresser ändras inte förrän du ändrar dem eller nätverksgränssnittet tas bort. Du kan ändra tilldelningsmetoden när nätverksgränssnittet har skapats. Azure DHCP-servern tilldelar den här adressen till nätverksgränssnittet i den virtuella datorns operativsystem.|
    |Nätverkssäkerhetsgrupp|Inga| Lämna inställt på **Ingen**, välj en befintlig [nätverkssäkerhetsgrupp](security-overview.md)eller [skapa en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md). Med nätverkssäkerhetsgrupper kan du filtrera nätverkstrafik in och ut ur ett nätverksgränssnitt. Du kan använda noll eller en nätverkssäkerhetsgrupp på ett nätverksgränssnitt. Noll eller en nätverkssäkerhetsgrupp kan också tillämpas på det undernät som nätverksgränssnittet har tilldelats. När en nätverkssäkerhetsgrupp tillämpas på ett nätverksgränssnitt och undernätet som nätverksgränssnittet tilldelas uppstår ibland oväntade resultat. Mer om du vill felsöka nätverkssäkerhetsgrupper som tillämpas på nätverksgränssnitt och undernät finns i [Felsöka nätverkssäkerhetsgrupper](diagnose-network-traffic-filter-problem.md).|
    |Prenumeration|Ja|Välj en av dina [Azure-prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Den virtuella datorn som du ansluter ett nätverksgränssnitt till och det virtuella nätverk som du ansluter den till måste finnas i samma prenumeration.|
    |Privat IP-adress (IPv6)|Inga| Om du markerar den här kryssrutan tilldelas en IPv6-adress nätverksgränssnittet, utöver den IPv4-adress som tilldelats nätverksgränssnittet. I den här artikeln finns i avsnittet IPv6 i den här artikeln för viktig information om användning av IPv6 med nätverksgränssnitt. Du kan inte välja en tilldelningsmetod för IPv6-adressen. Om du väljer att tilldela en IPv6-adress tilldelas den med den dynamiska metoden.
    |IPv6-namn (visas bara när kryssrutan **Privat IP-adress (IPv6)** är markerad) |Ja, om kryssrutan **Privat IP-adress (IPv6)** är markerad.| Det här namnet tilldelas en sekundär IP-konfiguration för nätverksgränssnittet. Mer information om IP-konfigurationer finns i [Visa inställningar för nätverksgränssnitt](#view-network-interface-settings).|
    |Resursgrupp|Ja|Markera en befintlig [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eller skapa en. Det finns ett nätverksgränssnitt i samma eller en annan resursgrupp än den virtuella dator som du ansluter det till, eller det virtuella nätverk som du ansluter det till.|
    |Location|Ja|Den virtuella datorn som du ansluter ett nätverksgränssnitt till och det virtuella nätverk som du ansluter den till måste finnas på samma [plats,](https://azure.microsoft.com/regions)även kallad en region.|

Portalen ger inte möjlighet att tilldela en offentlig IP-adress till nätverksgränssnittet när du skapar den, även om portalen skapar en offentlig IP-adress och tilldelar den till ett nätverksgränssnitt när du skapar en virtuell dator med hjälp av portalen. Mer information om hur du lägger till en offentlig IP-adress i nätverksgränssnittet när du har skapat den finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md). Om du vill skapa ett nätverksgränssnitt med en offentlig IP-adress måste du använda CLI eller PowerShell för att skapa nätverksgränssnittet.

Portalen ger inte möjlighet att tilldela nätverksgränssnittet till programsäkerhetsgrupper när du skapar ett nätverksgränssnitt, men Det gör Azure CLI och PowerShell. Du kan tilldela ett befintligt nätverksgränssnitt till en programsäkerhetsgrupp med hjälp av portalen, men så länge nätverksgränssnittet är anslutet till en virtuell dator. Mer information om hur du tilldelar ett nätverksgränssnitt till en programsäkerhetsgrupp finns i [Lägga till eller ta bort från programsäkerhetsgrupper](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure tilldelar en MAC-adress till nätverksgränssnittet först efter att nätverksgränssnittet är anslutet till en virtuell dator och den virtuella datorn startas första gången. Du kan inte ange den MAC-adress som Azure tilldelar nätverksgränssnittet. MAC-adressen förblir tilldelad nätverksgränssnittet tills nätverksgränssnittet tas bort eller den privata IP-adressen som tilldelats den primära IP-konfigurationen för det primära nätverksgränssnittet ändras. Mer information om IP-adresser och IP-konfigurationer finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md)

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Visa inställningar för nätverksgränssnitt

Du kan visa och ändra de flesta inställningar för ett nätverksgränssnitt när det har skapats. Portalen visar inte DNS-suffixet eller medlemskap i programsäkerhetsgruppen för nätverksgränssnittet. Du kan använda PowerShell- eller Azure [CLI-kommandona](#view-settings-commands) för att visa DNS-suffixet och medlemskap i programsäkerhetsgrupp.

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj det nätverksgränssnitt som du vill visa eller ändra inställningar för i listan.
3. Följande objekt visas för det nätverksgränssnitt du valt:
   - **Översikt:** Innehåller information om nätverksgränssnittet, till exempel de IP-adresser som tilldelats det, det virtuella nätverket/undernätet som nätverksgränssnittet tilldelas och den virtuella datorn som nätverksgränssnittet är anslutet till (om det är kopplat till en). Följande bild visar översiktsinställningarna för ett nätverksgränssnitt med ![namnet **mywebserver256**: Översikt över nätverksgränssnitt](./media/virtual-network-network-interface/nic-overview.png)

     Du kan flytta ett nätverksgränssnitt till en annan resursgrupp eller prenumeration genom att välja **(ändra)** bredvid **resursgruppen** eller **prenumerationsnamnet**. Om du flyttar nätverksgränssnittet måste du flytta alla resurser som är relaterade till nätverksgränssnittet med det. Om nätverksgränssnittet är kopplat till en virtuell dator, till exempel, måste du också flytta den virtuella datorn och andra virtuella datorrelaterade resurser. Information om hur du flyttar ett nätverksgränssnitt finns i [Flytta resurs till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). I artikeln visas förutsättningar och hur du flyttar resurser med Azure-portalen, PowerShell och Azure CLI.
   - **IP-konfigurationer:** Offentliga och privata IPv4- och IPv6-adresser som tilldelats IP-konfigurationer visas här. Om en IPv6-adress tilldelas en IP-konfiguration visas inte adressen. Mer information om IP-konfigurationer och hur du lägger till och tar bort IP-adresser finns i [Konfigurera IP-adresser för ett Azure-nätverksgränssnitt](virtual-network-network-interface-addresses.md). IP-vidarebefordran och nättilldelning är också konfigurerade i det här avsnittet. Mer information om de här inställningarna finns i [Aktivera eller inaktivera IP-vidarekoppling](#enable-or-disable-ip-forwarding) och [Ändra undernättilldelning](#change-subnet-assignment).
   - **DNS-servrar:** Du kan ange vilken DNS-server ett nätverksgränssnitt tilldelas av Azure DHCP-servrarna. Nätverksgränssnittet kan ärva inställningen från det virtuella nätverket som nätverksgränssnittet har tilldelats eller ha en anpassad inställning som åsidosätter inställningen för det virtuella nätverk som det är tilldelat till. Information om hur du ändrar vad som visas finns i [Ändra DNS-servrar](#change-dns-servers).
   - **Nätverkssäkerhetsgrupp (NSG):** Visar vilken NSG som är associerad med nätverksgränssnittet (om sådan finns). En NSG innehåller inkommande och utgående regler för att filtrera nätverkstrafik för nätverksgränssnittet. Om en NSG är associerad med nätverksgränssnittet visas namnet på den associerade NSG.If an NSG is associated to the network interface, the name of the associated NSG is displayed. Om du vill ändra vad som visas läser [du Associera eller koppla bort en nätverkssäkerhetsgrupp](#associate-or-dissociate-a-network-security-group).
   - **Egenskaper:** Visar viktiga inställningar om nätverksgränssnittet, inklusive dess MAC-adress (tom om nätverksgränssnittet inte är anslutet till en virtuell dator) och prenumerationen den finns i.
   - **Effektiva säkerhetsregler:**  Säkerhetsregler visas om nätverksgränssnittet är kopplat till en virtuell dator som körs och en NSG är kopplad till nätverksgränssnittet, undernätet som det har tilldelats eller båda. Mer information om vad som visas finns i [Visa effektiva säkerhetsregler](#view-effective-security-rules). Mer information om NSG finns i [Nätverkssäkerhetsgrupper](security-overview.md).
   - **Effektiva rutter:** Vägar visas om nätverksgränssnittet är kopplat till en virtuell dator som körs. Flödena är en kombination av Azure-standardvägarna, alla användardefinierade vägar och alla BGP-vägar som kan finnas för undernätet som nätverksgränssnittet har tilldelats. Mer information om vad som visas finns i [Visa effektiva rutter](#view-effective-routes). Mer information om Standardvägar för Azure och användardefinierade vägar finns i [Översikt över Routning](virtual-networks-udr-overview.md).
Vanliga Azure Resource Manager-inställningar: Mer information om vanliga Azure Resource Manager-inställningar finns i [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md), [Åtkomstkontroll (IAM),](../role-based-access-control/overview.md) [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)och [Automation-skript](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Kommandon**

Om en IPv6-adress tilldelas ett nätverksgränssnitt returnerar PowerShell-utdata det faktum att adressen är tilldelad, men den tilldelade adressen returneras inte. På samma sätt returnerar CLI det faktum att adressen är tilldelad, men returnerar *null* i utdata för adressen.

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverksniska lista](/cli/azure/network/nic) för att visa nätverksgränssnitt i abonnemanget. [az nätverksniska visa](/cli/azure/network/nic) för att visa inställningar för ett nätverksgränssnitt|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) för att visa nätverksgränssnitt i prenumerations- eller visningsinställningarna för ett nätverksgränssnitt|

## <a name="change-dns-servers"></a>Ändra DNS-servrar

DNS-servern tilldelas av Azure DHCP-servern till nätverksgränssnittet i operativsystemet för den virtuella datorn. Den DNS-server som tilldelats är vad DNS-serverinställningen är för ett nätverksgränssnitt. Mer information om inställningar för namnmatchning för ett nätverksgränssnitt finns i [Namnmatchning för virtuella datorer](virtual-networks-name-resolution-for-vms-and-role-instances.md). Nätverksgränssnittet kan ärva inställningarna från det virtuella nätverket eller använda sina egna unika inställningar som åsidosätter inställningen för det virtuella nätverket.

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj det nätverksgränssnitt som du vill ändra en DNS-server för i listan.
3. Välj **DNS-servrar** under **INSTÄLLNINGAR**.
4. Välj antingen:
   - **Ärva från virtuellt nätverk:** Välj det här alternativet om du vill ärva den DNS-serverinställning som definierats för det virtuella nätverket som nätverksgränssnittet har tilldelats. På den virtuella nätverksnivån definieras antingen en anpassad DNS-server eller den AZURE-tillförda DNS-servern. Dns-servern som tillhandahålls av Azure kan lösa värdnamn för resurser som tilldelats samma virtuella nätverk. FQDN måste användas för att matcha resurser som tilldelats olika virtuella nätverk.
   - **Anpassad:** Du kan konfigurera din egen DNS-server för att matcha namn i flera virtuella nätverk. Ange IP-adressen för den server som du vill använda som DNS-server. Den DNS-serveradress som du anger tilldelas endast det här nätverksgränssnittet och åsidosätter alla DNS-inställningar för det virtuella nätverket som nätverksgränssnittet har tilldelats.
     >[!Note]
     >Om den virtuella datorn använder ett nätverkskort som ingår i en tillgänglighetsuppsättning, ärvs alla DNS-servrar som har angetts för var och en av de virtuella datorerna från alla nätverkskort som ingår i tillgänglighetsuppsättningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic uppdatering](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivera eller inaktivera IP-vidarebefordran

IP-vidarebefordran gör det möjligt för den virtuella datorn att ett nätverksgränssnitt är kopplat till:
- Ta emot nätverkstrafik som inte är avsedd för någon av de IP-adresser som tilldelats någon av de IP-konfigurationer som tilldelats nätverksgränssnittet.
- Skicka nätverkstrafik med en annan käll-IP-adress än den som tilldelats en av ett nätverksgränssnitts IP-konfigurationer.

Inställningen måste vara aktiverad för alla nätverksgränssnitt som är anslutna till den virtuella datorn som tar emot trafik som den virtuella datorn behöver vidarebefordra. En virtuell dator kan vidarebefordra trafik oavsett om den har flera nätverksgränssnitt eller ett enda nätverksgränssnitt kopplat till den. Medan IP-vidarebefordran är en Azure-inställning måste den virtuella datorn också köra ett program som kan vidarebefordra trafiken, till exempel brandvägg, WAN-optimering och belastningsutjämningsprogram. När en virtuell dator kör nätverksprogram kallas den virtuella datorn ofta för en virtuell nätverksinstallation. Du kan visa en lista över redo att distribuera virtuella nätverksinstallationer på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-vidarebefordran används vanligtvis med användardefinierade vägar. Mer information om användardefinierade vägar finns i [Användardefinierade vägar](virtual-networks-udr-overview.md).

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj det nätverksgränssnitt som du vill aktivera eller inaktivera IP-vidarebefordran för.
3. Välj **IP-konfigurationer** i avsnittet **INSTÄLLNINGAR.**
4. Välj **Aktiverad** eller **Inaktiverad** (standardinställning) om du vill ändra inställningen.
5. Välj **Spara**.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic uppdatering](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Ändra undernätstilldelning

Du kan ändra undernätet, men inte det virtuella nätverket, som ett nätverksgränssnitt har tilldelats.

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj det nätverksgränssnitt som du vill ändra undernätstilldelning för.
3. Välj **IP-konfigurationer** under **INSTÄLLNINGAR**. Om några privata IP-adresser för alla IP-konfigurationer som anges har **(Statisk)** bredvid dem, måste du ändra IP-adresstilldelningsmetoden till dynamisk genom att slutföra stegen som följer. Alla privata IP-adresser måste tilldelas med den dynamiska tilldelningsmetoden för att ändra nättilldelningen för nätverksgränssnittet. Om adresserna tilldelas med den dynamiska metoden fortsätter du till steg fem. Om några IPv4-adresser tilldelas med den statiska tilldelningsmetoden utför du följande steg för att ändra tilldelningsmetoden till dynamisk:
   - Välj den IP-konfiguration som du vill ändra IPv4-adresstilldelningsmetoden för i listan över IP-konfigurationer.
   - Välj **Dynamisk** för metoden för privat IP-adresstilldelning. **Assignment** Du kan inte tilldela en IPv6-adress med den statiska tilldelningsmetoden.
   - Välj **Spara**.
4. Markera det undernät som du vill flytta nätverksgränssnittet till från listrutan **Undernät.**
5. Välj **Spara**. Nya dynamiska adresser tilldelas från undernätsadressintervallet för det nya undernätet. När du har tilldelat nätverksgränssnittet till ett nytt undernät kan du tilldela en statisk IPv4-adress från det nya undernätsadressintervallet om du vill. Mer information om hur du lägger till, ändrar och tar bort IP-adresser för ett nätverksgränssnitt finns i [Hantera IP-adresser](virtual-network-network-interface-addresses.md).

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic ip-config uppdatering](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Lägga till eller ta bort från programsäkerhetsgrupper

Du kan bara lägga till ett nätverksgränssnitt i eller ta bort ett nätverksgränssnitt från en programsäkerhetsgrupp som använder portalen om nätverksgränssnittet är kopplat till en virtuell dator. Du kan använda PowerShell eller Azure CLI för att lägga till ett nätverksgränssnitt i eller ta bort ett nätverksgränssnitt från en programsäkerhetsgrupp, oavsett om nätverksgränssnittet är anslutet till en virtuell dator eller inte. Läs mer om [programsäkerhetsgrupper](security-overview.md#application-security-groups) och hur du [skapar en programsäkerhetsgrupp](manage-network-security-group.md).

1. I rutan *Sökresurser, tjänster och dokument* högst upp i portalen börjar du skriva namnet på en virtuell dator som har ett nätverksgränssnitt som du vill lägga till i eller ta bort från en programsäkerhetsgrupp. När namnet på den virtuella datorn visas i sökresultaten markerar du det.
2. Under **INSTÄLLNINGAR** väljer du **Nätverk**.  Välj **Programsäkerhetsgrupper** och konfigurera sedan **programsäkerhetsgrupperna**för att välja de programsäkerhetsgrupper som du vill lägga till nätverksgränssnittet i eller avmarkera de programsäkerhetsgrupper som du vill ta bort nätverksgränssnittet från och välj sedan **Spara**. Endast nätverksgränssnitt som finns i samma virtuella nätverk kan läggas till i samma programsäkerhetsgrupp. Programsäkerhetsgruppen måste finnas på samma plats som nätverksgränssnittet.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic uppdatering](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associera eller koppla bort en nätverkssäkerhetsgrupp

1. Ange *nätverksgränssnitt* i sökrutan i sökrutan högst upp på portalen. När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Välj nätverksgränssnittet i listan som du vill koppla en nätverkssäkerhetsgrupp till eller ta bort en nätverkssäkerhetsgrupp från.
3. Välj **Nätverkssäkerhetsgrupp** under **INSTÄLLNINGAR**.
4. Välj **Redigera**.
5. Välj **Nätverkssäkerhetsgrupp** och välj sedan den nätverkssäkerhetsgrupp som du vill associera till nätverksgränssnittet, eller välj **Ingen**, om du vill koppla bort en nätverkssäkerhetsgrupp.
6. Välj **Spara**.

**Kommandon**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Ta bort ett nätverksgränssnitt

Du kan ta bort ett nätverksgränssnitt så länge det inte är kopplat till en virtuell dator. Om ett nätverksgränssnitt är anslutet till en virtuell dator måste du först placera den virtuella datorn i tillståndet stoppad (frigörd) och sedan koppla från nätverksgränssnittet från den virtuella datorn. Om du vill koppla från ett nätverksgränssnitt från en virtuell dator slutför du stegen i [Koppla från ett nätverksgränssnitt från en virtuell dator](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Du kan inte koppla från ett nätverksgränssnitt från en virtuell dator om det är det enda nätverksgränssnitt som är kopplat till den virtuella datorn. En virtuell dator måste alltid ha minst ett nätverksgränssnitt kopplat till den. Om du tar bort en virtuell dator tas alla nätverksgränssnitt som är anslutna till den bort, men nätverksgränssnitten tas inte bort.

1. Skriv *nätverksgränssnitt*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **nätverksgränssnitt** visas i sökresultaten markerar du det.
2. Markera nätverksgränssnittet i listan som du vill ta bort.
3. Under **Översikt** Välj **Ta bort**.
4. Välj **Ja** för att bekräfta borttagning av nätverksgränssnittet.

När du tar bort ett nätverksgränssnitt släpps alla MAC- eller IP-adresser som tilldelats det.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az nätverk nic ta bort](/cli/azure/network/nic)|
|PowerShell|[Ta bort-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Lösa anslutningsproblem

Om du inte kan kommunicera med eller från en virtuell dator kan säkerhetsregler eller vägar för nätverkssäkerhetsgrupper som gäller för ett nätverksgränssnitt orsaka problemet. Du har följande alternativ som hjälper dig att lösa problemet:

### <a name="view-effective-security-rules"></a>Visa effektiva säkerhetsregler

De gällande säkerhetsreglerna för varje nätverksgränssnitt som är kopplat till en virtuell dator är en kombination av de regler som du har skapat i en nätverkssäkerhetsgrupp och [standardsäkerhetsregler](security-overview.md#default-security-rules). Om du förstår de effektiva säkerhetsreglerna för ett nätverksgränssnitt kan du avgöra varför du inte kan kommunicera med eller från en virtuell dator. Du kan visa gällande regler för alla nätverksgränssnitt som är kopplade till en virtuell dator som körs.

1. I sökrutan högst upp på portalen anger du namnet på en virtuell dator som du vill visa effektiva säkerhetsregler för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten markerar du den och väljer sedan en virtuell dator i listan.
2. Välj **Nätverk** under **INSTÄLLNINGAR**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **Effektiva säkerhetsregler** under **SUPPORT + FELSÖKNING**.
5. Granska listan över effektiva säkerhetsregler för att avgöra om det finns rätt regler för inkommande och utgående kommunikation. Läs mer om vad du ser i listan i [Översikt över nätverkssäkerhetsgrupper](security-overview.md).

Funktionen för IP-flödeskontroll i Azure Network Watcher kan också hjälpa dig att avgöra om säkerhetsregler förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Mer information finns i [IP-flödeskontroll](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az nätverk nic lista-effektiv-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Visa effektiva rutter

De effektiva vägarna för nätverksgränssnitten som är kopplade till en virtuell dator är en kombination av standardvägar, alla vägar som du har skapat och alla vägar som sprids från lokala nätverk via BGP via en Azure-gateway för virtuella nätverk. Om du förstår vilka effektiva vägar som är effektiva för ett nätverksgränssnitt kan du avgöra varför du inte kan kommunicera med eller från en virtuell dator. Du kan visa de effektiva vägarna för alla nätverksgränssnitt som är kopplade till en virtuell dator som körs.

1. I sökrutan högst upp på portalen anger du namnet på en virtuell dator som du vill visa effektiva säkerhetsregler för. Om du inte känner till namnet på en virtuell dator anger du *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten markerar du den och väljer sedan en virtuell dator i listan.
2. Välj **Nätverk** under **INSTÄLLNINGAR**.
3. Välj namnet på ett nätverksgränssnitt.
4. Välj **Effektiva vägar** under SUPPORT + **FELSÖKNING**.
5. Granska listan över effektiva vägar för att avgöra om det finns rätt vägar för din obligatoriska inkommande och utgående kommunikation. Läs mer om vad du ser i listan i [översikten Över routning](virtual-networks-udr-overview.md).

Nästa hop-funktion i Azure Network Watcher kan också hjälpa dig att avgöra om vägar förhindrar kommunikation mellan en virtuell dator och en slutpunkt. Mer information finns i [Nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Kommandon**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzeffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i nätverksgränssnitt måste ditt konto tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas rätt behörigheter i följande tabell:

| Åtgärd                                                                     | Namn                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hämta nätverksgränssnitt                                     |
| Microsoft.Network/networkInterfaces/write                                  | Skapa eller uppdatera nätverksgränssnitt                        |
| Microsoft.Network/networkInterfaces/join/action                            | Koppla ett nätverksgränssnitt till en virtuell dator           |
| Microsoft.Network/networkInterfaces/delete                                 | Ta bort nätverksgränssnitt                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Gå med i en resurs till ett nätverksgränssnitt via en servi ...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Skaffa nätverksgränssnitt effektiv rutttabell               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Skaffa nätverksgränssnittseffektiva säkerhetsgrupper           |
| Microsoft.Network/networkInterfaces/loadBalancers/read Microsoft.Network/networkInterfaces/loadBalancers/read Microsoft.Network/networkInterfaces/loadBalancers/read Microsoft.                     | Hämta belastningsutjämnare för nätverksgränssnitt                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read Microsoft.Network/networkInterfaces/serviceAssociations/read Microsoft.Network/networkInterfaces/serviceAssociations/read Microsoft.               | Hämta serviceföreningen                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Skapa eller uppdatera en tjänstassociation                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete Microsoft.Network/networkInterfaces/serviceAssociations/delete Microsoft.Network/networkInterfaces/serviceAssociations/delete Microsoft.             | Ta bort tjänstassociation                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Verifiera tjänstassociation                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read Microsoft.Network/networkInterfaces/ipconfigurations/read Microsoft.Network/networkInterfaces/ipconfigurations/read Microsoft.                  | Skaffa IP-konfiguration för nätverksgränssnitt                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en virtuell dator med flera nätverkskort med [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Skapa en virtuell dator med ett nätverkskort med flera IPv4-adresser med [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) eller [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Skapa en virtuell dator med ett NIC-nätverkskort med en privat IPv6-adress (bakom en Azure Load Balancer) med [mallen](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Azure CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)eller Azure Resource Manager
- Skapa ett nätverksgränssnitt med [powershell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller med Azure [Resource Manager-mall](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk
