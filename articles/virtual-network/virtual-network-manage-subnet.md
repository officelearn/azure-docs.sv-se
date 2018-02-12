---
title: "Lägga till, ändra eller ta bort ett undernät för virtuella Azure-nätverket | Microsoft Docs"
description: "Lär dig mer om att lägga till, ändra eller ta bort ett undernät för virtuellt nätverk i Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: f8b60a27e760ae74c7f068844fad1ae0d4324366
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett undernät för virtuellt nätverk

Lär dig mer om att lägga till, ändra eller ta bort ett undernät för virtuellt nätverk. Om du inte är bekant med virtuella nätverk, innan du lägger till, ändra eller ta bort ett undernät, rekommenderar vi att du läser [översikt över Azure Virtual Network](virtual-networks-overview.md) och [skapa, ändra eller ta bort ett virtuellt nätverk](virtual-network-manage-network.md). Alla Azure-resurser som har distribuerats till ett virtuellt nätverk har distribuerats i ett undernät i ett virtuellt nätverk.
 
## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.2.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.26 eller senare. Kör `az --version` att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

## <a name="add-a-subnet"></a>Lägg till ett undernät

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten väljer den.
2. Välj det virtuella nätverket som du vill lägga till ett undernät till i listan över virtuella nätverk.
3. Under **inställningar**väljer **undernät**.
4. Välj **+ undernät**.
5. Ange värden för följande parametrar:
    - **Namnet**: namnet måste vara unika inom det virtuella nätverket.
    - **Adressintervall**: intervallet måste vara unika inom adressutrymmet för det virtuella nätverket. Intervallet får inte överlappa med andra undernät adressintervall i det virtuella nätverket. Adressutrymmet måste anges med hjälp av Classless Inter-Domain Routing CIDR-notering. Du kan till exempel definiera en undernätsadressutrymmet 10.0.0.0/24 i ett virtuellt nätverk med adressutrymme 10.0.0.0/16. Det minsta intervall som du kan ange är /29, vilket möjliggör åtta IP-adresser för undernätet. Azure reserverar de första och sista adressen i varje undernät för protokollet överensstämmelse. Tre ytterligare adresser är reserverade för användning i Azure-tjänsten. Därför definiera ett undernät med en /29 adressen resulterar i tre användbara IP-adresser i undernätet. Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway, måste du skapa en gateway-undernätet. Lär dig mer om [specifik adressintervallet överväganden för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adressintervallet när undernätet läggs vissa villkor. Information om hur du ändrar ett adressintervall för undernätet finns [ändra undernätsinställningar](#change-subnet-settings).
    - **Nätverkssäkerhetsgruppen**: du kan associera noll eller en befintlig säkerhetsgrupp för nätverk till ett undernät att filtrera inkommande och utgående nätverkstrafik för undernätet. Nätverkssäkerhetsgruppen måste finnas i samma prenumeration och plats som det virtuella nätverket. Lär dig mer om [nätverkssäkerhetsgrupper](security-overview.md) och [hur du skapar en nätverkssäkerhetsgrupp](virtual-networks-create-nsg-arm-pportal.md).
    - **Routningstabellen**: du kan associera noll eller en befintlig routningstabellen till ett undernät för att styra nätverksroutning trafik till andra nätverk. Routningstabellen måste finnas i samma prenumeration och plats som det virtuella nätverket. Lär dig mer om [Azure routning](virtual-networks-udr-overview.md) och [hur du skapar en routningstabell](create-user-defined-route-portal.md)
    - **Tjänstens slutpunkter:** ett undernät kan ha noll eller flera tjänstslutpunkter som aktiverats för den. Om du vill aktivera en tjänstslutpunkt för en tjänst, Välj den eller de tjänster som du vill aktivera Tjänsteslutpunkter för från den **Services** lista. Om du vill ta bort en tjänstslutpunkt avmarkerar du tjänsten som du vill ta bort tjänstslutpunkten för. Läs mer om Tjänsteslutpunkter i [översikt över virtuella nätverk service slutpunkter](virtual-network-service-endpoints-overview.md). När du aktiverar en tjänstslutpunkt för en tjänst, måste du också aktivera nätverksåtkomst för undernätet för en resurs som skapas med tjänsten. Om du aktiverar tjänstslutpunkten för till exempel *Microsoft.Storage*, måste du även aktivera nätverksåtkomst till alla Azure Storage-konton som du vill bevilja åtkomst till. Du har aktiverat tjänstslutpunkten för mer information om hur du aktivera nätverksåtkomst till undernät som en tjänstslutpunkt har aktiverats för finns i dokumentationen för enskild tjänst.
6. Om du vill lägga till undernätet i det virtuella nätverket som du har valt, Välj **OK**.

**Kommandon**

- Azure CLI: [skapa az undernät för virtuellt nätverk](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Ändra inställningar för undernätet

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten väljer den.
2. Välj det virtuella nätverket som innehåller det undernät som du vill ändra inställningarna för listan över virtuella nätverk.
3. Under **inställningar**väljer **undernät**.
4. Välj det undernät som du vill ändra inställningarna för i listan med undernät. Du kan ändra följande inställningar:

    - **Adressintervall:** om inga resurser har distribuerats i undernätet, kan du ändra adressintervallet. Om det finns några resurser på undernätet, måste du antingen flytta resurserna till ett annat undernät eller först bort dem från undernätet. De steg du vidta för att flytta eller ta bort en resurs varierar beroende på resursen. Läs i dokumentationen för varje resurs som du vill flytta eller ta bort information om hur du flyttar eller ta bort resurser som finns i undernät. Se begränsningarna för **adressintervall** i steg 5 i [Lägg till ett undernät](#add-a-subnet).
    - **Användare**: du kan styra åtkomsten till undernätet med hjälp av inbyggda roller eller dina egna anpassade roller. Mer information om hur du tilldelar roller och användare åtkomst till undernätet finns [använda rolltilldelning för att hantera åtkomst till resurserna i Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - Information om hur du ändrar **nätverkssäkerhetsgruppen**, **routningstabellen**, **användare**, och **tjänstens slutpunkter**, se steg 5 i [ Lägg till ett undernät](#add-a-subnet).
5. Välj **spara**.

**Kommandon**

- Azure CLI: [az network vnet undernät uppdatering](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan ta bort ett undernät om det finns inga resurser i undernätet. Om det finns resurser i undernät, måste du ta bort de resurser som finns i undernät innan du kan ta bort undernätet. De steg du vidta för att ta bort en resurs varierar beroende på resursen. Läs i dokumentationen för varje resurs som du vill ta bort om du vill veta hur du tar bort resurser som finns i undernät.

1. Skriv i sökrutan överst i portalen *virtuella nätverk* i sökrutan. När **virtuella nätverken** visas i sökresultaten väljer den.
2. Välj det virtuella nätverk som innehåller det undernät som du vill ta bort från listan över virtuella nätverk.
3. Under **inställningar**väljer **undernät**.
4. Välj i listan med undernät **...** , höger, för undernätet du vill ta bort
5. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az network vnet ta bort](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

Om du vill utföra aktiviteter på undernät måste ditt konto måste ha tilldelats den [network-deltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassade](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats rätt behörigheter som anges i följande tabell:

|Åtgärd                                                                |   Åtgärdsnamn                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Hämta undernät för virtuellt nätverk                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Skapa eller uppdatera undernät för virtuellt nätverk      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Ta bort undernät för virtuellt nätverk                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Ansluta virtuella nätverk                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Ansluta till tjänsten till ett undernät                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Hämta virtuella datorer i virtuellt undernät  |
