---
title: "Skapa, ändra eller ta bort en Azure-nätverket | Microsoft Docs"
description: "Lär dig mer om att skapa, ändra eller ta bort ett virtuellt nätverk i Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="create-change-or-delete-a-virtual-network"></a>Skapa, ändra eller ta bort ett virtuellt nätverk

Lär dig mer om att skapa och ta bort ett virtuellt nätverk och ändra inställningar som DNS-servrar och IP-adressutrymmen för ett befintligt virtuellt nätverk.

Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av Azure-molnet som är dedikerad till din Azure-prenumeration. För varje virtuellt nätverk som du skapar, kan du:
- Välj ett adressutrymme för att tilldela. Ett adressutrymme består av en eller flera adressintervall som definierats med Classless Inter-Domain Routing CIDR-notation som 10.0.0.0/16.
- Välj att använda Azure-tillhandahållna DNS-servern eller använda DNS-servern. Alla resurser som är anslutna till det virtuella nätverket har tilldelats den här DNS-servern att matcha namn i det virtuella nätverket.
- Segmentera det virtuella nätverket i undernät, var och en med sin egen adressintervall inom adressutrymmet för det virtuella nätverket. Om du vill lära dig mer om att skapa, ändra och ta bort undernät, se [Lägg till, ändra eller ta bort undernät](virtual-network-manage-subnet.md).

Den här artikeln beskriver hur du skapar, ändra och ta bort virtuella nätverk med hjälp av Azure Resource Manager-distributionsmodellen.

## <a name="before"></a>Innan du börjar

Innan du börjar de uppgifter som beskrivs i den här artikeln måste uppfylla följande krav:

- Om du inte har använt för att arbeta med virtuella nätverk, rekommenderar vi att du läser övningen i [skapa ditt första Azure-nätverk](virtual-network-get-started-vnet-subnet.md). Den här övningen hjälper dig att bekanta dig med virtuella nätverk.
- Mer information om begränsningar för virtuella nätverk, granska [Azure gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Logga in på Azure-portalen, Azure kommandoradsverktyget (Azure CLI) eller Azure PowerShell med hjälp av Azure-konto. Om du inte har ett Azure-konto kan du registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du planerar att använda PowerShell-kommandon för att slutföra de uppgifter som beskrivs i den här artikeln, måste du först [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure PowerShell-cmdlets som är installerad. Om du vill få hjälp med PowerShell-kommandon i exemplen, ange `get-help <command> -full`.
- Om du planerar att använda Azure CLI-kommandona för att slutföra de uppgifter som beskrivs i den här artikeln, måste du först [installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI är installerad. Om du vill ha hjälp med Azure CLI-kommandon, ange `az <command> --help`.


## <a name="create-vnet"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Klicka på **ny** > **nätverk** > **för virtuella nätverk**.
3. På den **för virtuella nätverk** blad i den **Välj en distributionsmodell** och lämna **Resource Manager** markerad och klicka sedan på **skapa**.
4. På den **skapa virtuellt nätverk** bladet ange eller Välj värden för följande inställningar och sedan klickar du på **skapa**:
    - **Namnet**: namnet måste vara unikt i den [resursgruppen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) att du väljer för att skapa det virtuella nätverket i. Du kan inte ändra namnet när det virtuella nätverket har skapats. Du kan skapa flera virtuella nätverk över tid. Namnge förslag finns [namngivningskonventioner](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Följa en namngivningskonvention hjälper gör det enklare att hantera flera virtuella nätverk.
    - **Adressutrymmet**: Ange adressutrymmet i CIDR-notering. Det adressutrymme som du definierar kan vara public eller private (RFC 1918). Om du definierar adressutrymmet som offentligt eller privat kan adressutrymmet nås från inom ett virtuellt nätverk och virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande adressutrymmen:
        - 224.0.0.0/4 (Multicast)
        - 255.255.255.255/32 (Broadcast)
        - 127.0.0.0/8 (Loopback)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (interna DNS)

      Även om du kan definiera bara ett adressutrymme när du skapar det virtuella nätverket, kan du lägga till flera adressutrymmen när det virtuella nätverket har skapats. Information om hur du lägger till ett adressutrymme för ett befintligt virtuellt nätverk finns [Lägg till eller ta bort ett adressutrymme](#add-address-spaces) i den här artikeln.

      >[!WARNING]
      >Om ett virtuellt nätverk har adressutrymmen som överlappar ett annat virtuellt nätverk eller lokala nätverk, går inte att ansluta de två nätverken. Innan du definierar ett adressutrymme, fundera på om du kanske vill ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk i framtiden.
      >
      >

    - **Undernätnamnet**: undernätnamnet måste vara unika inom det virtuella nätverket. Du kan inte ändra undernätnamnet efter undernätet skapas. Portalen måste du definiera ett undernät när du skapar ett virtuellt nätverk, även om ett virtuellt nätverk är inte ha några undernät. Du kan definiera bara ett undernät när du skapar ett virtuellt nätverk i portalen. Du kan lägga till flera undernät till det virtuella nätverket senare, när det virtuella nätverket har skapats. Om du vill lägga till ett undernät till ett virtuellt nätverk, se [skapar du ett undernät](virtual-network-manage-subnet.md#create-subnet) i [skapa, ändra eller ta bort undernät](virtual-network-manage-subnet.md). Du kan skapa ett virtuellt nätverk som har flera undernät med hjälp av Azure CLI eller PowerShell.

      >[!TIP]
      >Ibland kan skapa administratörer olika undernät för att filtrera eller kontrollera routning av nätverkstrafik mellan undernäten. Innan du definierar undernät, fundera på hur du vill filtrera och vidarebefordra trafik mellan dina undernät. Mer information om hur du filtrerar trafik mellan undernät finns [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md). Azure automatiskt vägar trafik mellan undernät, men du kan åsidosätta Azure standardvägar. Information om hur du åsidosätter Azure undernät trafik standardroutning finns [användardefinierade vägar](virtual-networks-udr-overview.md).
      >

    - **Adressintervall för gatewayundernät**: intervallet måste vara inom adressutrymmet som du angav för det virtuella nätverket. Det minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollet överensstämmelse. Tre ytterligare adresser är reserverade för användning i Azure-tjänsten. Därför har ett virtuellt nätverk med ett adressintervall för undernätet för /29 endast tre användbara IP-adresser. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa en gateway-undernätet. Lär dig mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet efter undernätet skapas vissa villkor. Information om hur du ändrar ett adressintervall för undernätet finns [ändra undernätsinställningar](#change-subnet) i [Lägg till, ändra eller ta bort undernät](virtual-network-manage-subnet.md).
    - **Prenumerationen**: Välj en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Du kan inte använda samma virtuella nätverk i mer än en Azure-prenumeration. Du kan dock ansluta ett virtuellt nätverk i en prenumeration till virtuella nätverk i andra prenumerationer. Använd Azure VPN-Gateway eller peering virtuellt nätverk för att ansluta virtuella nätverk för olika prenumerationer. Azure-resurser som du ansluter till det virtuella nätverket måste vara i samma prenumeration som det virtuella nätverket.
    - **Resursgruppen**: Välj en befintlig [resursgruppen](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) eller skapa en ny. En Azure-resurs som du ansluter till det virtuella nätverket kan vara i samma resursgrupp som det virtuella nätverket eller i en annan resursgrupp.
    - **Plats**: Välj en Azure [plats](https://azure.microsoft.com/regions/), även kallad en region. Ett virtuellt nätverk kan vara i endast en Azure-plats. Du kan dock inte ansluta ett virtuellt nätverk på en plats till ett virtuellt nätverk på en annan plats med hjälp av en VPN-gateway. Azure-resurser som du ansluter till det virtuella nätverket måste vara på samma plats som det virtuella nätverket.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[Skapa AZ network vnet](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Ny AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Visa virtuella nätverk och inställningar

Visa virtuella nätverk och inställningar:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten klickar du på **virtuella nätverken**.
3. På den **virtuella nätverken** bladet, klickar du på det virtuella nätverket som du vill visa inställningarna för.
4. Följande inställningar finns i bladet för det virtuella nätverket som du har valt:
    - **Översikt över**: innehåller information om virtuellt nätverk, inklusive adressutrymme och DNS-servrar. Följande skärmbild visar en översikt över inställningar för ett virtuellt nätverk med namnet **MyVNet**:

        ![Översikt över nätverk-gränssnitt](./media/virtual-network-manage-network/vnet-overview.png)

      På den **översikt** bladet kan du flytta ett virtuellt nätverk till en annan grupp av prenumerationen eller resursen. Information om hur du flyttar ett virtuellt nätverk finns [flytta resurser till en annan resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artikeln visar en lista över förutsättningar och hur du flyttar resurser med hjälp av Azure-portalen, PowerShell och Azure CLI. Alla resurser som är anslutna till det virtuella nätverket måste flyttas tillsammans med det virtuella nätverket.
    - **Adressutrymmet**: adressutrymmen som har tilldelats det virtuella nätverket visas. Information om hur du lägger till och ta bort ett adressutrymme för att slutföra stegen i [Lägg till eller ta bort ett adressutrymme](#address-spaces) i den här artikeln.
    - **Anslutna enheter**: visas alla resurser som är anslutna till det virtuella nätverket. I den föregående skärmbilden ansluten tre nätverksgränssnitt och en belastningsutjämnare till det virtuella nätverket. Nya resurser som du skapar och ansluta till det virtuella nätverket visas. Om du tar bort en resurs som var ansluten till det virtuella nätverket visas den inte längre i listan.
    - **Undernät**: visas en lista över undernät som finns i det virtuella nätverket. Om du vill lära dig mer om att lägga till och ta bort ett undernät, se [skapar du ett undernät](virtual-network-manage-subnet.md#create-subnet) och [tar bort ett undernät](virtual-network-manage-subnet.md#delete-subnet) i [Lägg till, ändra eller ta bort undernät](virtual-network-manage-subnet.md).
    - **DNS-servrar**: du kan ange om Azure interna DNS-server eller en anpassad DNS-server ger namnmatchning för enheter som är anslutna till det virtuella nätverket. När du skapar ett virtuellt nätverk med hjälp av Azure portal används Azure DNS-servrar för namnmatchning i ett virtuellt nätverk som standard. Om du vill ändra DNS-servrar måste du slutföra stegen i [Lägg till, ändra eller ta bort en DNS-server](#dns-servers) i den här artikeln.
    - **Peerkopplingar**: om det finns befintliga peerkopplingar i prenumerationen, anges här. Du kan visa inställningarna för befintliga peerkopplingar eller skapa, ändra eller ta bort peerkopplingar. Läs mer om peerkopplingar i [virtuella nätverk peering](virtual-network-peering-overview.md).
    - **Egenskaper för**: Visar inställningar om det virtuella nätverket, inklusive resurs-ID för det virtuella nätverket och Azure-prenumerationen i.
    - **Diagram över**: diagrammet innehåller en bild av alla enheter som är anslutna till det virtuella nätverket. Diagrammet har vissa viktig information om enheterna. Om du vill hantera en enhet i den här vyn i diagrammet, klickar du på enheten.
    - **Gemensamma inställningar för Azure**: Mer information om gemensamma inställningar för Azure finns följande information:
        *   [Aktivitetslogg](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Taggar](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automation-skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[AZ network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Lägg till eller ta bort ett adressutrymme

Du kan lägga till och ta bort adressutrymmen för ett virtuellt nätverk. Ett adressutrymme måste anges i CIDR-notation och kan inte överlappa andra adressutrymmen i samma virtuella nätverk. Du definierar adressutrymmen kan vara public eller private (RFC 1918). Om du definierar adressutrymmet som offentligt eller privat kan adressutrymmet nås från inom ett virtuellt nätverk och virtuella nätverk och från alla lokala nätverk som du har anslutit till det virtuella nätverket. Du kan inte lägga till följande adressutrymmen:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Broadcast)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (interna DNS)

Lägga till eller ta bort ett adressutrymme:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten väljer **virtuella nätverken**.
3. På den **virtuella nätverken** bladet, klickar du på det virtuella nätverket som du vill lägga till eller ta bort ett adressutrymme.
4. På den virtuella nätverk bladet under **inställningar**, klickar du på **adressutrymmet**.
5. På bladet för adressutrymmet, gör du något av följande alternativ:
    - **Lägg till ett adressutrymme**: Ange nytt adressutrymme. Adressutrymmet kan inte överlappa ett befintligt adressutrymme som definieras för det virtuella nätverket.
    - **Ta bort ett adressutrymme**: Högerklicka på ett adressutrymme och klicka sedan på **ta bort**. Om det finns ett undernät i adressutrymmet, kan du inte ta bort adressutrymmet. Om du vill ta bort ett adressutrymme, måste du först radera undernät (och alla resurser som är anslutna till undernäten) som finns i adressutrymmet.
6. Klicka på **Spara**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|Endast Resource Manager|[AZ network vnet uppdatering](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Lägga till, ändra eller ta bort en DNS-server

Alla virtuella datorer som är anslutna till virtuella nätverk registrera med DNS-servrar som du anger för det virtuella nätverket. De kan också använda den angivna DNS-servern för namnmatchning. Varje nätverksgränssnitt (NIC) på en virtuell dator kan ha sin egen DNS-serverinställningarna. Om ett nätverkskort har sin egen DNS-serverinställningarna, åsidosätta de DNS-serverinställningarna för det virtuella nätverket. Mer information om NIC DNS-inställningar finns [Network interface-aktiviteter och inställningar](virtual-network-network-interface.md#change-dns-servers). Mer information om namnmatchning för virtuella datorer och rollinstanser i Azure Cloud Services, se [namnmatchning för virtuella datorer och rollinstanser](virtual-networks-name-resolution-for-vms-and-role-instances.md). Om du vill lägga till, ändra eller ta bort en DNS-server:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten väljer **virtuella nätverken**.
3. På den **virtuella nätverken** bladet, klickar du på det virtuella nätverket som du vill ändra DNS-inställningarna för.
4. På den virtuella nätverk bladet under **inställningar**, klickar du på **DNS-servrar**.
5. Välj något av följande alternativ på bladet med en lista över DNS-servrar:
    - **Standard (Azure-tillhandahållna)**: alla resursnamn och privata IP-adresser registreras automatiskt till Azure DNS-servrar. Du kan lösa namn mellan alla resurser som är anslutna till samma virtuella nätverk. Du kan inte använda det här alternativet för att matcha namn i virtuella nätverk. Om du vill matcha namnen på virtuella nätverk, måste du använda en anpassad DNS-server.
    - **Anpassad**: du kan lägga till en eller flera servrar, upp till Azure gränsen för ett virtuellt nätverk. Läs mer om DNS-serverns gränser i [Azure gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Du har följande alternativ:
        - **Lägga till en adress**: lägger till servern i listan virtuellt nätverk DNS-servrar. Det här alternativet registrerar också DNS-server med Azure. Om du redan har registrerat en DNS-server med Azure kan du välja DNS-servern i listan.
        - **Ta bort en adress**: Klicka på Nästa för att den server som du vill ta bort **X**. Ta bort servern tar bort servern från den här listan över virtuella nätverk. DNS-servern är registrerad i Azure för ditt virtuella nätverk för att använda.
        - **Ändra ordning på DNS-serveradresser**: det är viktigt att kontrollera att du anger DNS-servrarna i rätt ordning för din miljö. DNS-serverlistor används i den ordning som de anges. De fungerar inte som en inställning för resursallokering. Om den första DNS-servern i listan kan nås, använder klienten att DNS-server, oavsett om DNS-servern fungerar korrekt. Ta bort alla DNS-servrar som listas och Lägg sedan till dem i den ordning som du vill.
        - **Ändra en adress**: Markera den DNS-servern i listan och ange sedan det nya namnet.
6. Klicka på **Spara**.
7. Starta om de virtuella datorerna som är anslutna till det virtuella nätverket, så tilldelas de nya DNS-serverinställningarna. Virtuella datorer fortsätta att använda deras aktuella DNS-inställningar förrän de startas om.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[AZ network vnet uppdatering](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Ta bort ett virtuellt nätverk

Du kan ta bort ett virtuellt nätverk endast om det finns inga resurser som är anslutna till den. Om det finns resurser som är anslutna till alla undernät i det virtuella nätverket, måste du först ta bort de resurser som är anslutna till alla undernät i det virtuella nätverket. De steg du vidta för att ta bort en resurs varierar beroende på resursen. Läs i dokumentationen för varje resurstyp av som du vill ta bort om du vill veta hur du tar bort resurser som är anslutna till undernät. Ta bort ett virtuellt nätverk:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som är tilldelade (minst) behörigheter för rollen Network-deltagare för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten klickar du på **virtuella nätverken**.
3. På den **virtuella nätverken** bladet Välj det virtuella nätverk som du vill ta bort.
4. På bladet för virtuella nätverk för att bekräfta att det finns inga enheter anslutna till det virtuella nätverket under **inställningar**, klickar du på **anslutna enheter**. Om det finns anslutna enheter, måste du ta bort dem innan du kan ta bort det virtuella nätverket. Om det finns inga anslutna enheter, klickar du på **översikt**.
5. Överst på bladet klickar du på den **ta bort** ikon.
6. Bekräfta borttagningen av det virtuella nätverket, klicka på **Ja**.


**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[ta bort Azure-nätverk-vnet](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Ta bort-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Nästa steg

- Om du vill skapa en virtuell dator och koppla den till ett virtuellt nätverk, se [skapa ett virtuellt nätverk och ansluta virtuella datorer](virtual-network-get-started-vnet-subnet.md#create-vms).
- Om du vill filtrera nätverkstrafiken mellan undernät i ett virtuellt nätverk finns [skapa nätverkssäkerhetsgrupper](virtual-networks-create-nsg-arm-pportal.md).
- To-peer-ett virtuellt nätverk till ett annat virtuellt nätverk, se [skapa ett virtuellt nätverk som peering](virtual-network-create-peering.md#portal).
- Mer information om alternativ för att ansluta ett virtuellt nätverk till ett lokalt nätverk, se [om VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
