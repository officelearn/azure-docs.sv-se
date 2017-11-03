---
title: "Lägga till, ändra eller ta bort ett undernät för virtuella Azure-nätverket | Microsoft Docs"
description: "Lär dig mer om att lägga till, ändra eller ta bort ett undernät för virtuellt nätverk i Azure."
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
ms.openlocfilehash: 85ba6ef3e51c339a77eb9b4198c4f87e2a64cf09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett undernät för virtuellt nätverk

Lär dig mer om att lägga till, ändra eller ta bort ett undernät för virtuellt nätverk. 

Om du inte är bekant med virtuella nätverk, innan du lägger till, ändra eller ta bort ett undernät, rekommenderar vi att du läser [översikt över Azure Virtual Network](virtual-networks-overview.md) och [skapa, ändra eller ta bort ett virtuellt nätverk](virtual-network-manage-network.md). Alla Azure-resurser som har distribuerats till ett virtuellt nätverk har distribuerats i ett undernät i ett virtuellt nätverk. Flera undernät skapas vanligtvis inom ett virtuellt nätverk till:
- **Filtrera trafik mellan undernät**. Du kan använda nätverkssäkerhetsgrupper för undernät att filtrera inkommande och utgående nätverkstrafik för alla resurser (t.ex. virtuella datorer) som finns i det virtuella nätverket. Mer information om hur du skapar en nätverkssäkerhetsgrupp finns [skapa nätverkssäkerhetsgrupper](virtual-networks-create-nsg-arm-pportal.md).
- **Kontrollera routning mellan undernät**. Azure skapar standardvägar så att trafik dirigeras automatiskt mellan undernät. Du kan åsidosätta Azure standardvägar genom att skapa användardefinierade vägar. Mer information om användardefinierade vägar finns [skapar användardefinierade vägar](virtual-network-create-udr-arm-ps.md). 

Den här artikeln beskriver hur du lägger till, ändra och ta bort ett undernät för virtuella nätverk som har skapats med hjälp av Azure Resource Manager-distributionsmodellen.
 
## <a name="before"></a>Innan du börjar

Innan du börjar de uppgifter som beskrivs i den här artikeln måste uppfylla följande krav:

- Om du inte har använt för att arbeta med virtuella nätverk, rekommenderar vi att du läser övningen i [skapa ditt första Azure-nätverk](virtual-network-get-started-vnet-subnet.md). Den här övningen hjälper dig att bekanta dig med virtuella nätverk.
- Mer information om begränsningar för virtuella nätverk, granska [Azure gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Logga in på Azure-portalen, Azure kommandoradsverktyget (Azure CLI) eller Azure PowerShell med hjälp av Azure-konto. Om du inte har ett Azure-konto kan du registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du planerar att använda PowerShell-kommandon för att slutföra de uppgifter som beskrivs i den här artikeln, måste du först [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure PowerShell-cmdlets som är installerad. Om du vill få hjälp med PowerShell-kommandon i exemplen, ange `get-help <command> -full`.
- Om du planerar att använda Azure CLI-kommandona för att slutföra de uppgifter som beskrivs i den här artikeln måste du:
    - [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI är installerad.
    - Använd Azure-molnet Shell. Du kan använda Azure Cloud Shell istället för att installera CLI och dess beroenden. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell (**> _**) längst upp i Azure-portalen. 

  Om du vill ha hjälp med Azure CLI-kommandon, ange `az <command> --help`.

## <a name="create-subnet"></a>Lägg till ett undernät

Lägg till ett undernät:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten klickar du på **virtuella nätverken**.
3. På den **virtuella nätverken** bladet, klickar du på det virtuella nätverket som du vill lägga till ett undernät till.
4. Klicka på virtuellt nätverk-bladet **undernät**.
5. Klicka på **+ undernät**.
6. På den **Lägg till undernät** bladet ange värden för följande parametrar:
    - **Namnet**: namnet måste vara unika inom det virtuella nätverket.
    - **Adressintervall**: intervallet måste vara unika inom adressutrymmet för det virtuella nätverket. Intervallet får inte överlappa med andra undernät adressintervall i det virtuella nätverket. Adressutrymmet måste anges med hjälp av Classless Inter-Domain Routing CIDR-notering. Du kan till exempel definiera en undernätsadressutrymmet 10.0.0.0/24 i ett virtuellt nätverk med adressutrymme 10.0.0.0/16. Det minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollet överensstämmelse. Tre ytterligare adresser är reserverade för användning i Azure-tjänsten. Därför definiera ett undernät med en /29 adressen resulterar i tre användbara IP-adresser i undernätet. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa en gateway-undernätet. Lär dig mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet när undernätet läggs vissa villkor. Information om hur du ändrar ett adressintervall för undernätet finns [ändra undernätsinställningar](#change-subnet) i den här artikeln.
    - **Nätverkssäkerhetsgruppen**: Om du vill kan du kan koppla en befintlig säkerhetsgrupp för nätverk med undernät att kontrollera inkommande och utgående nätverkstrafik filtrering för undernätet. Nätverkssäkerhetsgruppen måste finnas i samma prenumeration och plats som det virtuella nätverket. Den måste också skapas med hjälp av Resource Manager-distributionsmodellen. Mer information om hur du skapar en nätverkssäkerhetsgrupp finns [Nätverkssäkerhetsgrupper](virtual-networks-create-nsg-arm-pportal.md).
    - **Routningstabellen**: (valfritt) du kan koppla en befintlig routningstabellen med undernät att styra nätverksroutning trafik till andra nätverk. Routningstabellen måste finnas i samma prenumeration och plats som det virtuella nätverket. Den måste också skapas med hjälp av Resource Manager-distributionsmodellen. Mer information om hur du skapar vägtabeller finns [användardefinierade vägar](virtual-network-create-udr-arm-ps.md).
    - **Användare**: du kan styra åtkomsten till undernätet med hjälp av inbyggda roller eller dina egna anpassade roller. Mer information om hur du tilldelar roller och användare åtkomst till undernätet finns [använda rolltilldelning för att hantera åtkomst till resurserna i Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Klicka på Lägg till undernätet i det virtuella nätverket som du har valt, **OK**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[Skapa AZ undernät för virtuellt nätverk](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Nya AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json), [lägga till AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Ändra inställningar för undernätet

Du kan ändra nätverkssäkerhetsgrupper och vägtabeller användaråtkomst till ett undernät med hantering av resurser som är i ett undernät. Mer information om de här inställningarna i [Lägg till ett undernät](#create-subnet), finns i steg 6. Om du vill ändra adressutrymmet för ett undernät måste du först ta bort alla resurser som finns i undernät. De steg du vidta för att ta bort en resurs varierar beroende på resursen. Läs i dokumentationen för varje resurs som du vill ta bort om du vill veta hur du tar bort resurser som finns i undernät. Ändra adressintervallet för ett undernät:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten klickar du på **virtuella nätverken**.
3. På den **virtuella nätverken** bladet, klickar du på det virtuella nätverket som du vill ändra ett adressintervall för undernätet.
4. Klicka på det undernät som du vill ändra adressintervallet.
5. I bladet undernät i den **adressintervall** ange nya adressintervallet. Intervallet måste vara unika inom adressutrymmet för det virtuella nätverket. Intervallet får inte överlappa med andra undernät adressintervall i det virtuella nätverket. Adressutrymmet måste anges med hjälp av CIDR-notering. Du kan till exempel definiera en undernätsadressutrymmet 10.0.0.0/24 i ett virtuellt nätverk med adressutrymme 10.0.0.0/16. Det minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollet överensstämmelse. Tre ytterligare adresser är reserverade för användning i Azure-tjänsten. Därför kan ett undernät med en /29 adressintervall har tre användbara IP-adresser. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa en gateway-undernätet. Lär dig mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet efter undernätet skapas vissa villkor. Information om hur du ändrar ett adressintervall för undernätet finns [ändra undernätsinställningar](#change-subnet) i den här artikeln.
6. Klicka på **Spara**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[AZ network vnet undernät uppdatering](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Ange AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Ta bort ett undernät

Du kan ta bort ett undernät om det finns inga resurser i undernätet. Om det finns resurser i undernät, måste du ta bort de resurser som finns i undernät innan du kan ta bort undernätet. De steg du vidta för att ta bort en resurs varierar beroende på resursen. Läs i dokumentationen för varje resurs som du vill ta bort om du vill veta hur du tar bort resurser som finns i undernät. Ta bort ett undernät:

1. Logga in på den [portal](https://portal.azure.com) med ett konto som har tilldelats behörigheter för nätverket deltagarrollen (minst) för din prenumeration. Mer information om hur du tilldelar roller och behörigheter till konton finns [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv i sökrutan portal **virtuella nätverk**. I sökresultaten klickar du på **virtuella nätverken**.
3. På den **virtuella nätverken** bladet, klickar du på det virtuella nätverket som du vill ta bort ett undernät.
4. På den virtuella nätverk bladet under **inställningar**, klickar du på **undernät**.
5. Högerklicka på undernät som du vill ta bort, klicka i listan med undernät som visas på undernät-bladet, **ta bort**, och klicka sedan på **Ja** ta bort undernätet.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|Azure CLI|[ta bort AZ nätverks-virtuella nätverk](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Ta bort AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Nästa steg

För att skapa en virtuell dator i ett undernät, se [skapa ett virtuellt nätverk och distribuera virtuella datorer i undernätet](virtual-network-get-started-vnet-subnet.md#create-vms).
