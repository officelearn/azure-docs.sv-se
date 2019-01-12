---
title: Skapa, ändra eller ta bort ett Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig mer om att skapa, ändra eller ta bort ett virtuellt nätverk i Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: jdial
ms.openlocfilehash: a43988f8d4d6ae318f409cf1e79d8ad2ff8c8af1
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247834"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Skapa, ändra eller ta bort ett virtuellt nätverk

Lär dig mer om att skapa och ta bort ett virtuellt nätverk och ändra inställningar, t.ex. DNS-servrar och IP-adressutrymmen för ett befintligt virtuellt nätverk. Om du är nybörjare på virtuella nätverk kan du läsa mer om dem i den [översikt över virtuella nätverk](virtual-networks-overview.md) eller genom att slutföra en [självstudien](quick-create-portal.md). Ett virtuellt nätverk innehåller undernät. Om du vill lära dig mer om att skapa, ändra och ta bort undernät, se [hantera undernät](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudiekursen kräver Azure PowerShell-modulen version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.
- Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** > **nätverk** > **virtuellt nätverk**.
2. Ange eller Välj värden för följande inställningar och välj sedan **skapa**:
    - **Namn**: Namnet måste vara unikt i den [resursgrupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) att du väljer för att skapa det virtuella nätverket i. Du kan inte ändra namnet när det virtuella nätverket har skapats. Du kan skapa flera virtuella nätverk över tid. Namnge förslag, finns i [namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Följa en namngivningskonvention kan göra det enklare att hantera flera virtuella nätverk.
    - **Adressutrymme**: Adressutrymmet för ett virtuellt nätverk består av en eller flera inte överlappar adressintervallen som anges i CIDR-notation. Adressintervallet som du definierar kan vara offentlig eller privat (RFC 1918). Om du definierar adressintervallet som offentliga eller privata kan adressintervallet som nås från i det virtuella nätverket från sammankopplade virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande-adressintervall:
        - 224.0.0.0/4 (Multicast)
        - 255.255.255.255/32 (Broadcast)
        - 127.0.0.0/8 (Loopback)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (internt DNS)

      Även om du kan definiera endast en adressintervall när du skapar det virtuella nätverket, du kan lägga till flera adressintervall adressutrymmet när det virtuella nätverket har skapats. Läs hur du lägger till ett adressintervall till ett befintligt virtuellt nätverk i [Lägg till eller ta bort ett adressintervall](#add-or-remove-an-address-range).

      >[!WARNING]
      >Om ett virtuellt nätverk har adressintervall som överlappar ett annat virtuellt nätverk eller lokalt nätverk, går inte att ansluta de två nätverken. Innan du definierar ett adressintervall kan du överväga om du kanske vill ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk i framtiden.
      >
      >

    - **Namn på undernät**: Namnet på undernätet måste vara unikt inom det virtuella nätverket. Du kan inte ändra namnet på undernätet efter undernätet har skapats. Portalen måste du definiera ett undernät när du skapar ett virtuellt nätverk, även om ett virtuellt nätverk inte har inga undernät. Du kan definiera endast en undernät när du skapar ett virtuellt nätverk i portalen. Du kan lägga till flera undernät för det virtuella nätverket senare, när det virtuella nätverket har skapats. Om du vill lägga till ett undernät till ett virtuellt nätverk, se [hantera undernät](virtual-network-manage-subnet.md). Du kan skapa ett virtuellt nätverk som har flera undernät med hjälp av Azure CLI eller PowerShell.

      >[!TIP]
      >Ibland kan skapa administratörer olika undernät för att filtrera eller har kontroll över routning av nätverkstrafik mellan undernät. Innan du definierar undernät du fundera över hur du filtrerar och dirigera trafik mellan dina undernät. Mer information om hur du filtrerar trafik mellan undernät finns [Nätverkssäkerhetsgrupper](security-overview.md). Azure automatiskt dirigerar trafik mellan undernät, men du kan åsidosätta de standardvägar som Azure. Läs mer om Azures standard undernät-trafikdirigering i [routningsöversikten](virtual-networks-udr-overview.md).
      >

    - **Adressintervall för undernätet**: Intervallet måste ligga inom det adressutrymme som du angav för det virtuella nätverket. Den minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollöverensstämmelse. Tre ytterligare adresser är reserverade för användning av Azure-tjänsten. Därför har ett virtuellt nätverk med ett adressintervall för undernätet som/29 endast tre användbara IP-adresser. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa ett gateway-undernät. Läs mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet när undernätet har skapats kan vissa villkor. Läs hur du ändrar ett adressintervall för undernätet i [hantera undernät](virtual-network-manage-subnet.md).
    - **Prenumeration**: Välj en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Du kan inte använda samma virtuella nätverk i flera Azure-prenumeration. Men du kan ansluta ett virtuellt nätverk i en prenumeration till virtuella nätverk i andra prenumerationer med [virtuell nätverkspeering](virtual-network-peering-overview.md). Alla Azure-resurser som du ansluter till det virtuella nätverket måste vara i samma prenumeration som det virtuella nätverket.
    - **Resursgrupp**: Välj en befintlig [resursgrupp](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) eller skapa en ny. En Azure-resurs som du ansluter till det virtuella nätverket kan vara i samma resursgrupp som det virtuella nätverket eller i en annan resursgrupp.
    - **Plats**: Välj en Azure [plats](https://azure.microsoft.com/regions/), även kallad en region. Ett virtuellt nätverk kan vara i endast en Azure-plats. Dock kan du ansluta ett virtuellt nätverk på en plats till ett virtuellt nätverk på en annan plats med hjälp av en VPN-gateway. Alla Azure-resurser som du ansluter till det virtuella nätverket måste finnas på samma plats som det virtuella nätverket.

**Kommandon**

- Azure CLI: [az network vnet skapa](/cli/azure/network/vnet)
- PowerShell: [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Visa virtuella nätverk och inställningar

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill visa inställningarna för listan över virtuella nätverk.
3. Följande inställningar visas för det virtuella nätverket som du har valt:
    - **Översikt över**: Innehåller information om det virtuella nätverket, inklusive adressutrymmet och DNS-servrar. Följande skärmbild visar översikt över inställningar för ett virtuellt nätverk med namnet **MyVNet**:

        ![Översikt över Network-gränssnitt](./media/manage-virtual-network/vnet-overview.png)

      Du kan flytta ett virtuellt nätverk till en annan prenumeration eller resursgrupp grupp genom att välja **ändra** bredvid **resursgrupp** eller **prenumerationsnamn**. Läs hur du flyttar ett virtuellt nätverk i [flytta resurser till en annan resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artikeln innehåller förutsättningar och hur du flyttar resurser med Azure-portalen, PowerShell och Azure CLI. Alla resurser som är anslutna till det virtuella nätverket måste flytta med det virtuella nätverket.
    - **Adressutrymme**: Adressutrymmen som är kopplade till det virtuella nätverket visas. Om du vill lära dig mer om att lägga till och ta bort ett adressintervall till adressutrymmet, slutför du stegen i [Lägg till eller ta bort ett adressintervall](#add-or-remove-an-address-range).
    - **Anslutna enheter**: Alla resurser som är anslutna till det virtuella nätverket finns. I föregående skärmbild, är tre nätverksgränssnitt och en belastningsutjämnare anslutna till det virtuella nätverket. Nya resurser som du skapar och ansluter till det virtuella nätverket visas. Om du tar bort en resurs som anslöts till det virtuella nätverket visas den inte längre i listan.
    - **Undernät**: En lista med undernät som finns i det virtuella nätverket visas. Om du vill lära dig mer om att lägga till och ta bort ett undernät, se [hantera undernät](virtual-network-manage-subnet.md).
    - **DNS-servrar**: Du kan ange om Azure intern DNS-server eller en anpassad DNS-server ger namnmatchning för enheter som är anslutna till det virtuella nätverket. När du skapar ett virtuellt nätverk med hjälp av Azure-portalen används Azure DNS-servrar för namnmatchning i ett virtuellt nätverk som standard. Om du vill ändra DNS-servrar måste du slutföra stegen i [ändra DNS-servrar](#change-dns-servers) i den här artikeln.
    - **Peerkopplingar**: Om det finns befintliga peerings i prenumeration, visas de här. Du kan visa inställningarna för befintliga peerings, eller skapa, ändra eller ta bort peerings. Läs mer om peer-kopplingar i [peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).
    - **Egenskaper för**: Visar inställningar för det virtuella nätverket, inklusive resurs-ID för det virtuella nätverket och den är i den Azure-prenumeration.
    - **Diagram över**: Diagrammet innehåller en visuell representation av alla enheter som är anslutna till det virtuella nätverket. Diagrammet har vissa viktig information om enheterna. Välj enheten för att hantera en enhet i den här vyn i diagram.
    - **Gemensamma inställningar för Azure**: Mer information om gemensamma inställningar för Azure finns följande information:
        *   [Aktivitetslogg](../azure-monitor/platform/activity-logs-overview.md)
        *   [Åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automationsskript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Kommandon**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Lägg till eller ta bort ett adressintervall

Du kan lägga till och ta bort adressintervall för ett virtuellt nätverk. Ett adressintervall måste anges i CIDR-notation och får inte överlappa med andra adressintervall inom samma virtuella nätverk. Adressintervallen som du definierar kan vara offentlig eller privat (RFC 1918). Om du definierar adressintervallet som offentliga eller privata kan adressintervallet som nås från i det virtuella nätverket från sammankopplade virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. 

Om du inte har några undernät som är associerade med den kan du minska adressintervallet för ett virtuellt nätverk. Annars kan kan du bara förlänga adressintervall, till exempel ändra en /16 till/8 som. Du kan börja med en liten adressintervall, och sedan utöka det senare eller Lägg till ytterligare adressintervall.

<!-- the last two sentences above are added per GitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Du kan inte lägga till följande-adressintervall:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Broadcast)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (internt DNS)

Lägga till eller ta bort ett adressintervall:

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill lägga till eller ta bort ett adressintervall i listan med virtuella nätverk.
3. Välj **adressutrymme**under **inställningar**.
4. Gör något av följande alternativ:
    - **Lägg till ett adressintervall**: Ange det nya adressintervallet. Adressintervallet får inte överlappa med befintliga adressintervallet som har definierats för det virtuella nätverket.
    - **Ta bort ett adressintervall**: Till höger i adressintervall som du vill ta bort, Välj **...** och välj sedan **ta bort**. Om det finns ett undernät i adressintervallet, kan du inte ta bort adressintervallet. Om du vill ta bort ett adressintervall, måste du först radera alla undernät (och alla resurser i undernät) som finns i adressintervallet.
5. Välj **Spara**.

**Kommandon**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Ändra DNS-servrar

Alla virtuella datorer som är anslutna till virtuella nätverk registrera med DNS-servrar som du anger för det virtuella nätverket. De kan också använda den angivna DNS-servern för namnmatchning. Varje nätverksgränssnitt (NIC) på en virtuell dator kan ha en egen DNS-serverinställningarna. Om ett nätverkskort har en egen DNS-serverinställningarna kan åsidosätta de DNS-serverinställningarna för det virtuella nätverket. Mer information om NIC-DNS-inställningar finns [Network interface-uppgifter och inställningar](virtual-network-network-interface.md#change-dns-servers). Mer information om namnmatchning för virtuella datorer och rollinstanser i Azure Cloud Services, finns i [namnmatchning för virtuella datorer och rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md). Om du vill lägga till, ändra eller ta bort en DNS-server:

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill ändra DNS-servrar för i listan med virtuella nätverk.
3.  Välj **DNS-servrar**under **inställningar**.
4. Välj något av följande alternativ:
    - **Standard (medföljer Azure)**: Alla resursnamn och privata IP-adresser registreras automatiskt till Azure DNS-servrar. Du kan matcha namn mellan alla resurser som är anslutna till samma virtuella nätverk. Du kan inte använda det här alternativet för att matcha namn för virtuella nätverk. Om du vill matcha namnen för virtuella nätverk, måste du använda en anpassad DNS-server.
    - **Anpassat**: Du kan lägga till en eller flera servrar, upp till Azure gränsen för ett virtuellt nätverk. Läs mer om DNS-serverns gränser i [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Du har följande alternativ:
        - **Lägga till en adress**: Lägger till servern i listan virtuellt nätverk DNS-servrar. Det här alternativet kan du även registrerar DNS-servern med Azure. Om du redan har registrerat en DNS-server med Azure kan du välja den DNS-servern i listan.
        - **Ta bort en adress**: Markera bredvid den server som du vill ta bort **...** , sedan **ta bort**. Tar bort servern tar bort servern från den här listan över virtuella nätverk. DNS-servern är fortfarande registrerade i Azure för dina virtuella nätverk för att använda.
        - **Ändra ordning på DNS-serveradresser**: Det är viktigt att verifiera att du lista dina DNS-servrar i rätt ordning för din miljö. DNS-serverlistor används i den ordning som de anges. De fungerar inte som en resursallokering-installationsprogrammet. Om den första DNS-servern i listan kan nås, använder klienten den DNS-servern, oavsett om DNS-servern fungerar korrekt. Ta bort alla DNS-servrar som listas och Lägg sedan till dem i den ordning som du vill.
        - **Ändra en adress**: Markera DNS-servern i listan och sedan ange den nya adressen.
5. Välj **Spara**.
6. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket, så tilldelas de nya DNS-serverinställningarna. Virtuella datorer fortsätta att använda deras aktuella DNS-inställningar förrän de startas om.

**Kommandon**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Du kan ta bort ett virtuellt nätverk endast om det finns inga resurser som är anslutna till den. Om det finns resurser som är anslutna till alla undernät i det virtuella nätverket, måste du först ta bort de resurser som är anslutna till alla undernät i det virtuella nätverket. Vilka steg du utför för att ta bort en resurs kan variera beroende på resurs. Om du vill lära dig mer om att ta bort resurser som är anslutna till undernät, Läs i dokumentationen för varje resurstyp som du vill ta bort. Ta bort ett virtuellt nätverk:

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
2. Välj det virtuella nätverket som du vill ta bort från listan över virtuella nätverk.
3. Bekräfta att det finns inga enheter som är anslutna till det virtuella nätverket genom att välja **anslutna enheter**under **inställningar**. Om det finns anslutna enheter, måste du ta bort dem innan du kan ta bort det virtuella nätverket. Om det finns inga anslutna enheter, väljer **översikt**.
4. Välj **Ta bort**.
5. För att bekräfta borttagningen av det virtuella nätverket, Välj **Ja**.

**Kommandon**

- Azure CLI: [azure network vnet ta bort](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Behörigheter

Om du vill genomföra åtgärder på virtuella nätverk, måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som visas i följande tabell:

| Åtgärd                                  |   Namn                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Läsa ett virtuellt nätverk              |
|Microsoft.Network/virtualNetworks/write  |   Skapa eller uppdatera ett virtuellt nätverk  |
|Microsoft.Network/virtualNetworks/delete |   Ta bort ett virtuellt nätverk            |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk