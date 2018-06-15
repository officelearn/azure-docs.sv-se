---
title: Skapa, ändra eller ta bort en Azure-nätverket | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort ett virtuellt nätverk i Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 56839c38de135a805c51bb96ad5d7abc41ebcad7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895445"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Skapa, ändra eller ta bort ett virtuellt nätverk

Lär dig mer om att skapa och ta bort ett virtuellt nätverk och ändra inställningar som DNS-servrar och IP-adressutrymmen för ett befintligt virtuellt nätverk. Om du har använt virtuella nätverk, kan du läsa mer om dem i den [översikt över virtuella nätverk](virtual-networks-overview.md) eller genom att slutföra en [kursen](quick-create-portal.md). Ett virtuellt nätverk innehåller undernät. Om du vill lära dig mer om att skapa, ändra och ta bort undernät, se [hantera undernät](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.
- Kontot du loggar in, eller Anslut till Azure med, måste vara tilldelade till den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** > **nätverk** > **för virtuella nätverk**.
2. Ange eller Välj värden för följande inställningar och sedan **skapa**:
    - **Namnet**: namnet måste vara unikt i den [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) att du väljer för att skapa det virtuella nätverket i. Du kan inte ändra namnet när det virtuella nätverket har skapats. Du kan skapa flera virtuella nätverk över tid. Namnge förslag finns [namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Följa en namngivningskonvention hjälper gör det enklare att hantera flera virtuella nätverk.
    - **Adressutrymmet**: adressutrymmet för ett virtuellt nätverk består av en eller flera icke-överlappande adressintervall som anges i CIDR-notering. Adressintervallet som du definierar kan vara public eller private (RFC 1918). Om du definierar adressintervallet som offentligt eller privat adressintervallet kan nås från inom ett virtuellt nätverk och virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande adressintervall:
        - 224.0.0.0/4 (Multicast)
        - 255.255.255.255/32 (Broadcast)
        - 127.0.0.0/8 (Loopback)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (interna DNS)

      Även om du kan definiera en enda adressintervall när du skapar det virtuella nätverket, du kan lägga till flera adressintervall adressutrymmet när det virtuella nätverket har skapats. Information om hur du lägger till ett adressintervall i ett befintligt virtuellt nätverk finns [Lägg till eller ta bort ett adressintervall](#add-or-remove-an-address-range).

      >[!WARNING]
      >Om ett virtuellt nätverk har adressintervall som överlappar ett annat virtuellt nätverk eller lokala nätverk, går inte att ansluta de två nätverken. Innan du definierar ett adressintervall fundera på om du kanske vill ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk i framtiden.
      >
      >

    - **Undernätnamnet**: undernätnamnet måste vara unika inom det virtuella nätverket. Du kan inte ändra undernätnamnet efter undernätet skapas. Portalen måste du definiera ett undernät när du skapar ett virtuellt nätverk, även om ett virtuellt nätverk är inte ha några undernät. Du kan definiera bara ett undernät när du skapar ett virtuellt nätverk i portalen. Du kan lägga till flera undernät till det virtuella nätverket senare, när det virtuella nätverket har skapats. Om du vill lägga till ett undernät till ett virtuellt nätverk, se [hantera undernät](virtual-network-manage-subnet.md). Du kan skapa ett virtuellt nätverk som har flera undernät med hjälp av Azure CLI eller PowerShell.

      >[!TIP]
      >Ibland kan skapa administratörer olika undernät för att filtrera eller kontrollera routning av nätverkstrafik mellan undernäten. Innan du definierar undernät, fundera på hur du vill filtrera och vidarebefordra trafik mellan dina undernät. Mer information om hur du filtrerar trafik mellan undernät finns [Nätverkssäkerhetsgrupper](security-overview.md). Azure automatiskt vägar trafik mellan undernät, men du kan åsidosätta Azure standardvägar. Läs mer om Azures undernät trafik standardroutning i [routning: översikt](virtual-networks-udr-overview.md).
      >

    - **Adressintervall för gatewayundernät**: intervallet måste vara inom adressutrymmet som du angav för det virtuella nätverket. Det minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollet överensstämmelse. Tre ytterligare adresser är reserverade för användning i Azure-tjänsten. Därför har ett virtuellt nätverk med ett adressintervall för undernätet för /29 endast tre användbara IP-adresser. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa en gateway-undernätet. Lär dig mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet efter undernätet skapas vissa villkor. Information om hur du ändrar ett adressintervall för undernätet finns [hantera undernät](virtual-network-manage-subnet.md).
    - **Prenumerationen**: Välj en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Du kan inte använda samma virtuella nätverk i mer än en Azure-prenumeration. Du kan dock inte ansluta ett virtuellt nätverk i en prenumeration till virtuella nätverk i andra prenumerationer med [virtuellt nätverk peering](virtual-network-peering-overview.md). Azure-resurser som du ansluter till det virtuella nätverket måste vara i samma prenumeration som det virtuella nätverket.
    - **Resursgruppen**: Välj en befintlig [resursgruppen](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) eller skapa en ny. En Azure-resurs som du ansluter till det virtuella nätverket kan vara i samma resursgrupp som det virtuella nätverket eller i en annan resursgrupp.
    - **Plats**: Välj en Azure [plats](https://azure.microsoft.com/regions/), även kallad en region. Ett virtuellt nätverk kan vara i endast en Azure-plats. Du kan dock inte ansluta ett virtuellt nätverk på en plats till ett virtuellt nätverk på en annan plats med hjälp av en VPN-gateway. Azure-resurser som du ansluter till det virtuella nätverket måste vara på samma plats som det virtuella nätverket.

**Kommandon**

- Azure CLI: [az network vnet skapa](/cli/azure/network/vnet)
- PowerShell: [nya-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Visa virtuella nätverk och inställningar

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill visa inställningarna för listan över virtuella nätverk.
3. Följande inställningar anges för det virtuella nätverket som du har valt:
    - **Översikt över**: innehåller information om virtuellt nätverk, inklusive adressutrymme och DNS-servrar. Följande skärmbild visar en översikt över inställningar för ett virtuellt nätverk med namnet **MyVNet**:

        ![Översikt över nätverk-gränssnitt](./media/manage-virtual-network/vnet-overview.png)

      Du kan flytta ett virtuellt nätverk till en annan grupp av prenumerationen eller resursen genom att välja **ändra** bredvid **resursgruppen** eller **prenumerationsnamn**. Information om hur du flyttar ett virtuellt nätverk finns [flytta resurser till en annan resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artikeln visar en lista över förutsättningar och hur du flyttar resurser med hjälp av Azure-portalen, PowerShell och Azure CLI. Alla resurser som är anslutna till det virtuella nätverket måste flyttas tillsammans med det virtuella nätverket.
    - **Adressutrymmet**: adressutrymmen som har tilldelats det virtuella nätverket visas. Om du vill lära dig mer om att lägga till och ta bort ett adressintervall till adressutrymmet, slutför du stegen i [Lägg till eller ta bort ett adressintervall](#add-or-remove-an-address-range).
    - **Anslutna enheter**: visas alla resurser som är anslutna till det virtuella nätverket. I den föregående skärmbilden ansluten tre nätverksgränssnitt och en belastningsutjämnare till det virtuella nätverket. Nya resurser som du skapar och ansluta till det virtuella nätverket visas. Om du tar bort en resurs som var ansluten till det virtuella nätverket kan den inte längre visas i listan.
    - **Undernät**: visas en lista över undernät som finns i det virtuella nätverket. Om du vill lära dig mer om att lägga till och ta bort ett undernät, se [hantera undernät](virtual-network-manage-subnet.md).
    - **DNS-servrar**: du kan ange om Azure interna DNS-server eller en anpassad DNS-server ger namnmatchning för enheter som är anslutna till det virtuella nätverket. När du skapar ett virtuellt nätverk med hjälp av Azure portal används Azure DNS-servrar för namnmatchning i ett virtuellt nätverk som standard. Om du vill ändra DNS-servrar måste du slutföra stegen i [ändra DNS-servrar](#change-dns-servers) i den här artikeln.
    - **Peerkopplingar**: om det finns befintliga peerkopplingar i prenumerationen, anges här. Du kan visa inställningarna för befintliga peerkopplingar eller skapa, ändra eller ta bort peerkopplingar. Läs mer om peerkopplingar i [virtuella nätverk peering](virtual-network-peering-overview.md).
    - **Egenskaper för**: Visar inställningar om det virtuella nätverket, inklusive resurs-ID för det virtuella nätverket och Azure-prenumerationen i.
    - **Diagram över**: diagrammet innehåller en bild av alla enheter som är anslutna till det virtuella nätverket. Diagrammet har vissa viktig information om enheterna. Välj enheten du vill hantera en enhet i den här vyn i diagrammet.
    - **Gemensamma inställningar för Azure**: Mer information om gemensamma inställningar för Azure finns följande information:
        *   [Aktivitetslogg](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automation-skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Kommandon**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Lägg till eller ta bort ett adressintervall

Du kan lägga till och ta bort adressintervall för ett virtuellt nätverk. Ett adressintervall måste anges i CIDR-notation och kan inte överlappa andra adressintervall inom samma virtuella nätverk. Adressintervall som du definierar kan vara public eller private (RFC 1918). Om du definierar adressintervallet som offentligt eller privat adressintervallet kan nås från inom ett virtuellt nätverk och virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande adressintervall:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Broadcast)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (interna DNS)

Lägga till eller ta bort ett adressintervall:

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill lägga till eller ta bort ett adressintervall i listan över virtuella nätverk.
3. Välj **adressutrymmet**under **inställningar**.
4. Gör något av följande alternativ:
    - **Lägga till ett adressintervall**: Ange nya adressintervall. Adressintervallet får inte överlappa med en befintlig adressintervall som definierats för det virtuella nätverket.
    - **Ta bort ett adressintervall**: till höger om adressintervallet som du vill ta bort, Välj **...** och välj **ta bort**. Om ett undernät som finns i adressintervallet, kan du inte ta bort adressintervallet. Om du vill ta bort ett adressintervall, måste du först ta bort undernät (och alla resurser i undernät) som finns i intervallet.
5. Välj **Spara**.

**Kommandon**

- Azure CLI: [az network vnet uppdatering](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Ändra DNS-servrar

Alla virtuella datorer som är anslutna till virtuella nätverk registrera med DNS-servrar som du anger för det virtuella nätverket. De kan också använda den angivna DNS-servern för namnmatchning. Varje nätverksgränssnitt (NIC) på en virtuell dator kan ha sin egen DNS-serverinställningarna. Om ett nätverkskort har sin egen DNS-serverinställningarna, åsidosätta de DNS-serverinställningarna för det virtuella nätverket. Mer information om NIC DNS-inställningar finns [Network interface-aktiviteter och inställningar](virtual-network-network-interface.md#change-dns-servers). Mer information om namnmatchning för virtuella datorer och rollinstanser i Azure Cloud Services, se [namnmatchning för virtuella datorer och rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md). Om du vill lägga till, ändra eller ta bort en DNS-server:

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill ändra DNS-servrar för i listan över virtuella nätverk.
3.  Välj **DNS-servrar**under **inställningar**.
4. Välj något av följande alternativ:
    - **Standard (Azure-tillhandahållna)**: alla resursnamn och privata IP-adresser registreras automatiskt till Azure DNS-servrar. Du kan lösa namn mellan alla resurser som är anslutna till samma virtuella nätverk. Du kan inte använda det här alternativet för att matcha namn i virtuella nätverk. Om du vill matcha namnen på virtuella nätverk, måste du använda en anpassad DNS-server.
    - **Anpassad**: du kan lägga till en eller flera servrar, upp till Azure gränsen för ett virtuellt nätverk. Läs mer om DNS-serverns gränser i [Azure gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Du har följande alternativ:
        - **Lägga till en adress**: lägger till servern i listan virtuellt nätverk DNS-servrar. Det här alternativet registrerar också DNS-server med Azure. Om du redan har registrerat en DNS-server med Azure kan du välja DNS-servern i listan.
        - **Ta bort en adress**: bredvid den server som du vill ta bort, Välj **...** , sedan **ta bort**. Ta bort servern tar bort servern från den här listan över virtuella nätverk. DNS-servern är registrerad i Azure för ditt virtuella nätverk för att använda.
        - **Ändra ordning på DNS-serveradresser**: det är viktigt att kontrollera att du anger DNS-servrarna i rätt ordning för din miljö. DNS-serverlistor används i den ordning som de anges. De fungerar inte som en inställning för resursallokering. Om den första DNS-servern i listan kan nås, använder klienten att DNS-server, oavsett om DNS-servern fungerar korrekt. Ta bort alla DNS-servrar som listas och Lägg sedan till dem i den ordning som du vill.
        - **Ändra en adress**: Markera DNS-servern i listan och sedan ange den nya adressen.
5. Välj **Spara**.
6. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket, så tilldelas de nya DNS-serverinställningarna. Virtuella datorer fortsätta att använda deras aktuella DNS-inställningar förrän de startas om.

**Kommandon**

- Azure CLI: [az network vnet uppdatering](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Du kan ta bort ett virtuellt nätverk endast om det finns inga resurser som är anslutna till den. Om det finns resurser som är anslutna till alla undernät i det virtuella nätverket, måste du först ta bort de resurser som är anslutna till alla undernät i det virtuella nätverket. De steg du vidta för att ta bort en resurs varierar beroende på resursen. Läs i dokumentationen för varje resurstyp av som du vill ta bort om du vill veta hur du tar bort resurser som är anslutna till undernät. Ta bort ett virtuellt nätverk:

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill ta bort från listan över virtuella nätverk.
3. Bekräfta att det finns inga enheter som är anslutna till det virtuella nätverket genom att välja **anslutna enheter**under **inställningar**. Om det finns anslutna enheter, måste du ta bort dem innan du kan ta bort det virtuella nätverket. Om det inte finns några anslutna enheter, Välj **översikt**.
4. Välj **Ta bort**.
5. För att bekräfta borttagningen av det virtuella nätverket, Välj **Ja**.

**Kommandon**

- Azure CLI: [azure network vnet ta bort](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Behörigheter

Om du vill utföra åtgärder på virtuella nätverk måste ditt konto måste ha tilldelats den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som anges i följande tabell:

| Åtgärd                                  |   Namn                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Läs ett virtuellt nätverk              |
|Microsoft.Network/virtualNetworks/write  |   Skapa eller uppdatera ett virtuellt nätverk  |
|Microsoft.Network/virtualNetworks/delete |   Ta bort ett virtuellt nätverk            |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och använda [Azure princip](policy-samples.md) för virtuella nätverk