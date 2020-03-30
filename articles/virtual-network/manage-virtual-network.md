---
title: Skapa, ändra eller ta bort ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar, ändrar eller tar bort ett virtuellt nätverk i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 70523dc12f3f20362fcf4a2c3cb456a182038e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280240"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Skapa, ändra eller ta bort ett virtuellt nätverk

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Lär dig hur du skapar och tar bort ett virtuellt nätverk och ändrar inställningar, till exempel DNS-servrar och IP-adressutrymmen, för ett befintligt virtuellt nätverk. Om du inte har tidigare i virtuella nätverk kan du läsa mer om dem i [översikten över virtuella nätverk](virtual-networks-overview.md) eller genom att fylla i en [självstudiekurs](quick-create-portal.md). Ett virtuellt nätverk innehåller undernät. Mer information om hur du skapar, ändrar och tar bort undernät finns i [Hantera undernät](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Innan du börjar

Slutför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto registrerar du dig för ett [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder https://portal.azure.comportalen öppnar och loggar du in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.
- Kontot som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ Skapa ett virtuellt nätverk för** > **resursnätverk** > **Virtual network**.
2. Ange eller välj värden för följande inställningar och välj sedan **Skapa:**
   - **Namn**: Namnet måste vara unikt i [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) som du väljer för att skapa det virtuella nätverket i. Du kan inte ändra namnet när det virtuella nätverket har skapats. Du kan skapa flera virtuella nätverk över tid. Namnförslag finns i [Namngivningskonventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Efter en namngivningskonvention kan det bli enklare att hantera flera virtuella nätverk.
   - **Adressutrymme**: Adressutrymmet för ett virtuellt nätverk består av ett eller flera adressintervall som inte överlappar varandra och som anges i CIDR-notation. Adressintervallet som du definierar kan vara offentligt eller privat (RFC 1918). Oavsett om du definierar adressintervallet som offentligt eller privat kan adressintervallet endast nås från det virtuella nätverket, från sammankopplade virtuella nätverk och från lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande adressintervall:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Sändning)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (Länk-lokal)
     - 168.63.129.16/32 [(hälsoavsökning av](../load-balancer/load-balancer-custom-probe-overview.md#probesource)intern DNS, DHCP och Azure Load Balancer)

     Även om du bara kan definiera ett adressintervall när du skapar det virtuella nätverket i portalen kan du lägga till fler adressintervall i adressutrymmet när det virtuella nätverket har skapats. Mer information om hur du lägger till ett adressintervall i ett befintligt virtuellt nätverk finns i [Lägga till eller ta bort ett adressintervall](#add-or-remove-an-address-range).

     >[!WARNING]
     >Om ett virtuellt nätverk har adressintervall som överlappar ett annat virtuellt nätverk eller lokalt nätverk kan de två nätverken inte anslutas. Innan du definierar ett adressintervall bör du överväga om du kanske vill ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk i framtiden.
     >
     >

     - **Undernätsnamn**: Undernätsnamnet måste vara unikt i det virtuella nätverket. Du kan inte ändra undernätsnamnet när undernätet har skapats. Portalen kräver att du definierar ett undernät när du skapar ett virtuellt nätverk, även om ett virtuellt nätverk inte behöver ha några undernät. I portalen kan du bara definiera ett undernät när du skapar ett virtuellt nätverk. Du kan lägga till fler undernät i det virtuella nätverket senare, när det virtuella nätverket har skapats. Mer om du vill lägga till ett undernät i ett virtuellt nätverk finns i [Hantera undernät](virtual-network-manage-subnet.md). Du kan skapa ett virtuellt nätverk som har flera undernät med hjälp av Azure CLI eller PowerShell.

       >[!TIP]
       >Ibland skapar administratörer olika undernät för att filtrera eller styra trafikdirigering mellan undernäten. Innan du definierar undernät bör du tänka på hur du kanske vill filtrera och dirigera trafik mellan undernäten. Mer information om hur du filtrerar trafik mellan undernät finns i [Nätverkssäkerhetsgrupper](security-overview.md). Azure dirigerar automatiskt trafik mellan undernät, men du kan åsidosätta Azure-standardvägar. Mer information om Azures standardroutning för undernätstrafik finns i [Översikt över Routning](virtual-networks-udr-overview.md).
       >

     - **Undernätsadressintervall:** Intervallet måste finnas inom det adressutrymme som du angav för det virtuella nätverket. Det minsta område du kan ange är /29, som innehåller åtta IP-adresser för undernätet. Azure reserverar den första och sista adressen i varje undernät för protokollkonformance. Ytterligare tre adresser är reserverade för Azure-tjänstanvändning. Därför har ett virtuellt nätverk med ett undernätsadressintervall på /29 bara tre användbara IP-adresser. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway måste du skapa ett gateway-undernät. Läs mer om [specifika överväganden om adressintervall för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet när undernätet har skapats under särskilda förhållanden. Mer information om hur du ändrar ett undernätsadressintervall finns i [Hantera undernät](virtual-network-manage-subnet.md).
     - **Prenumeration**: Välj en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Du kan inte använda samma virtuella nätverk i mer än en Azure-prenumeration. Du kan dock ansluta ett virtuellt nätverk i en prenumeration till virtuella nätverk i andra prenumerationer med [virtuell nätverks peering](virtual-network-peering-overview.md). Alla Azure-resurser som du ansluter till det virtuella nätverket måste finnas i samma prenumeration som det virtuella nätverket.
     - **Resursgrupp**: Välj en befintlig [resursgrupp](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) eller skapa en ny. En Azure-resurs som du ansluter till det virtuella nätverket kan finnas i samma resursgrupp som det virtuella nätverket eller i en annan resursgrupp.
     - **Plats**: Välj en [Azure-plats](https://azure.microsoft.com/regions/), även känd som en region. Ett virtuellt nätverk kan bara finnas på en Azure-plats. Du kan dock ansluta ett virtuellt nätverk på en plats till ett virtuellt nätverk på en annan plats med hjälp av en VPN-gateway. Alla Azure-resurser som du ansluter till det virtuella nätverket måste vara på samma plats som det virtuella nätverket.

**Kommandon**

- Azure CLI: [az nätverk vnet skapa](/cli/azure/network/vnet)
- PowerShell: [Nya AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Visa virtuella nätverk och inställningar

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på portalen. När **virtuella nätverk** visas i sökresultaten markerar du det.
2. Välj det virtuella nätverk som du vill visa inställningar för i listan över virtuella nätverk.
3. Följande inställningar visas för det virtuella nätverk du valt:
   - **Översikt**: Ger information om det virtuella nätverket, inklusive adressutrymme och DNS-servrar. Följande skärmbild visar översiktsinställningarna för ett virtuellt nätverk med namnet **MyVNet:**

     ![Översikt över nätverksgränssnitt](./media/manage-virtual-network/vnet-overview.png)

     Du kan flytta ett virtuellt nätverk till en annan prenumeration eller resursgrupp genom att välja **Ändra** bredvid **Resursgrupp** eller **Prenumerationsnamn**. Mer information om hur du flyttar ett virtuellt nätverk finns i [Flytta resurser till en annan resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). I artikeln visas förutsättningar och hur du flyttar resurser med hjälp av Azure-portalen, PowerShell och Azure CLI. Alla resurser som är anslutna till det virtuella nätverket måste flyttas med det virtuella nätverket.
   - **Adressutrymme**: De adressutrymmen som har tilldelats det virtuella nätverket visas. Om du vill veta hur du lägger till och tar bort ett adressintervall i adressutrymmet slutför du stegen i [Lägg till eller ta bort ett adressintervall](#add-or-remove-an-address-range).
   - **Anslutna enheter**: Alla resurser som är anslutna till det virtuella nätverket visas. I föregående skärmbild är tre nätverksgränssnitt och en belastningsutjämnare anslutna till det virtuella nätverket. Alla nya resurser som du skapar och ansluter till det virtuella nätverket visas. Om du tar bort en resurs som var ansluten till det virtuella nätverket visas den inte längre i listan.
   - **Undernät**: En lista över undernät som finns i det virtuella nätverket visas. Mer information om hur du lägger till och tar bort ett undernät finns i [Hantera undernät](virtual-network-manage-subnet.md).
   - **DNS-servrar**: Du kan ange om den interna DNS-servern i Azure eller en anpassad DNS-server ger namnmatchning för enheter som är anslutna till det virtuella nätverket. När du skapar ett virtuellt nätverk med hjälp av Azure-portalen används Azures DNS-servrar som standard för namnmatchning i ett virtuellt nätverk. Om du vill ändra DNS-servrarna utför du stegen i [Ändra DNS-servrar](#change-dns-servers) i den här artikeln.
   - **Peerings**: Om det finns befintliga peerings i prenumerationen, de visas här. Du kan visa inställningar för befintliga peerings eller skapa, ändra eller ta bort peerings. Mer information om peerings finns i [Virtual Network peering](virtual-network-peering-overview.md).
   - **Egenskaper**: Visar inställningar om det virtuella nätverket, inklusive det virtuella nätverkets resurs-ID och den Azure-prenumeration det finns i.
   - **Diagram**: Diagrammet ger en visuell representation av alla enheter som är anslutna till det virtuella nätverket. Diagrammet innehåller viktig information om enheterna. Om du vill hantera en enhet i den här vyn markerar du enheten i diagrammet.
   - **Vanliga Azure-inställningar:** Mer information om vanliga Azure-inställningar finns i följande information:
     - [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md)
     - [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
     - [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automationsskript](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Kommandon**

- Azure CLI: [az nätverk vnet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Lägga till eller ta bort ett adressintervall

Du kan lägga till och ta bort adressintervall för ett virtuellt nätverk. Ett adressintervall måste anges i CIDR-notation och kan inte överlappa med andra adressintervall inom samma virtuella nätverk. De adressintervall som du definierar kan vara offentliga eller privata (RFC 1918). Oavsett om du definierar adressintervallet som offentligt eller privat kan adressintervallet endast nås från det virtuella nätverket, från sammankopplade virtuella nätverk och från lokala nätverk som du har anslutit till det virtuella nätverket. 

Du kan minska adressintervallet för ett virtuellt nätverk så länge det fortfarande innehåller intervallen för associerade undernät. Dessutom kan du utöka adressintervallet, till exempel ändra en /16 till /8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Du kan inte lägga till följande adressintervall:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Sändning)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Länk-lokal)
- 168.63.129.16/32 [(hälsoavsökning av](../load-balancer/load-balancer-custom-probe-overview.md#probesource)intern DNS, DHCP och Azure Load Balancer)

Så här lägger du till eller tar bort ett adressintervall:

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på portalen. När **virtuella nätverk** visas i sökresultaten markerar du det.
2. Välj det virtuella nätverk som du vill lägga till eller ta bort ett adressintervall för i listan över virtuella nätverk.
3. Välj **Adressutrymme**under **INSTÄLLNINGAR**.
4. Slutför något av följande alternativ:
    - **Lägg till ett adressintervall**: Ange det nya adressintervallet. Adressintervallet kan inte överlappa med ett befintligt adressintervall som har definierats för det virtuella nätverket.
    - **Ta bort ett adressintervall:** Till höger om det adressintervall som du vill ta bort väljer du **...** och väljer sedan **Ta bort**. Om det finns ett undernät i adressintervallet kan du inte ta bort adressintervallet. Om du vill ta bort ett adressintervall måste du först ta bort alla undernät (och eventuella resurser i undernäten) som finns i adressintervallet.
5. Välj **Spara**.

**Kommandon**

- Azure CLI: [az nätverk vnet uppdatering](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Ändra DNS-servrar

Alla virtuella datorer som är anslutna till det virtuella nätverket registrerar sig med de DNS-servrar som du anger för det virtuella nätverket. De använder också den angivna DNS-servern för namnmatchning. Varje nätverksgränssnitt (NIC) i en virtuell dator kan ha sina egna DNS-serverinställningar. Om ett nätverkskort har egna DNS-serverinställningar åsidosätter de DNS-serverinställningarna för det virtuella nätverket. Mer information om DNS-inställningar för nätverkskort finns i [Nätverksgränssnittsuppgifter och inställningar](virtual-network-network-interface.md#change-dns-servers). Mer information om namnmatchning för virtuella datorer och rollinstanser i Azure Cloud Services finns i [Namnmatchning för virtuella datorer och rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md). Så här lägger du till, ändrar eller tar bort en DNS-server:

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på portalen. När **virtuella nätverk** visas i sökresultaten markerar du det.
2. Välj det virtuella nätverk som du vill ändra DNS-servrar för i listan över virtuella nätverk.
3. Välj **DNS-servrar**under **INSTÄLLNINGAR**.
4. Välj något av följande alternativ:
   - **Standard (Azure-tillhandahålls)**: Alla resursnamn och privata IP-adresser registreras automatiskt till Azure DNS-servrarna. Du kan matcha namn mellan alla resurser som är anslutna till samma virtuella nätverk. Du kan inte använda det här alternativet för att matcha namn över virtuella nätverk. Om du vill matcha namn i virtuella nätverk måste du använda en anpassad DNS-server.
   - **Anpassad:** Du kan lägga till en eller flera servrar, upp till Azure-gränsen för ett virtuellt nätverk. Mer information om DNS-servergränser finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Du har följande alternativ:
   - **Lägg till en adress:** Lägger till servern i listan över DNS-servrar i det virtuella nätverket. Det här alternativet registrerar också DNS-servern med Azure. Om du redan har registrerat en DNS-server med Azure kan du välja den DNS-servern i listan.
   - **Ta bort en adress:** Bredvid den server som du vill ta bort markerar du **...** och tar sedan **bort**. Om du tar bort servern tas endast servern bort från den här listan över virtuella nätverk. DNS-servern är fortfarande registrerad i Azure för att dina andra virtuella nätverk ska kunna användas.
   - **Ändra ordning på DNS-serveradresser:** Det är viktigt att du kontrollerar att du listar DNS-servrarna i rätt ordning för din miljö. DNS-serverlistor används i den ordning som de anges. De fungerar inte som en round-robin setup. Om den första DNS-servern i listan kan nås använder klienten den DNS-servern, oavsett om DNS-servern fungerar som den ska. Ta bort alla DNS-servrar som visas och lägg sedan till dem i den ordning du vill.
   - **Ändra en adress**: Markera DNS-servern i listan och ange sedan den nya adressen.
5. Välj **Spara**.
6. Starta om de virtuella datorer som är anslutna till det virtuella nätverket, så att de tilldelas de nya DNS-serverinställningarna. Virtuella datorer fortsätter att använda sina aktuella DNS-inställningar tills de startas om.

**Kommandon**

- Azure CLI: [az nätverk vnet uppdatering](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Du kan bara ta bort ett virtuellt nätverk om det inte finns några resurser anslutna till det. Om det finns resurser som är anslutna till ett undernät i det virtuella nätverket måste du först ta bort de resurser som är anslutna till alla undernät i det virtuella nätverket. Vilka åtgärder du vidtar för att ta bort en resurs varierar beroende på resursen. Om du vill veta hur du tar bort resurser som är anslutna till undernät läser du dokumentationen för varje resurstyp som du vill ta bort. Så här tar du bort ett virtuellt nätverk:

1. Ange *virtuella nätverk* i sökrutan i sökrutan högst upp på portalen. När **virtuella nätverk** visas i sökresultaten markerar du det.
2. Välj det virtuella nätverk som du vill ta bort i listan över virtuella nätverk.
3. Bekräfta att det inte finns några enheter anslutna till det virtuella nätverket genom att välja **Anslutna enheter**under **INSTÄLLNINGAR**. Om det finns anslutna enheter måste du ta bort dem innan du kan ta bort det virtuella nätverket. Om det inte finns några anslutna enheter väljer du **Översikt**.
4. Välj **Ta bort**.
5. Om du vill bekräfta borttagningen av det virtuella nätverket väljer du **Ja**.

**Kommandon**

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell: [Ta bort AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i virtuella nätverk måste ditt konto tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas lämpliga åtgärder i följande tabell:

| Åtgärd                                  |   Namn                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Läsa ett virtuellt nätverk              |
|Microsoft.Network/virtualNetworks/write  |   Skapa eller uppdatera ett virtuellt nätverk  |
|Microsoft.Network/virtualNetworks/delete |   Ta bort ett virtuellt nätverk            |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk med [PowerShell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk
