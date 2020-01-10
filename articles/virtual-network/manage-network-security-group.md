---
title: Skapa, ändra eller ta bort en Azure-nätverks säkerhets grupp
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar, ändrar eller tar bort en nätverks säkerhets grupp.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: d6c948c7fdfe2c5dc89c3e284ada33c2f7b542db
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750681"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Skapa, ändra eller ta bort en nätverks säkerhets grupp

Säkerhets regler i nätverks säkerhets grupper gör att du kan filtrera den typ av nätverks trafik som kan flöda in i och ut ur virtuella nätverks under nät och nätverks gränssnitt. Om du inte är bekant med nätverks säkerhets grupper kan du läsa [Översikt över nätverks säkerhets](security-overview.md) grupper för att lära dig mer om dem och slutföra självstudien [filtrera nätverks trafik](tutorial-filter-network-traffic.md) för att få lite erfarenhet av nätverks säkerhets grupper.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="work-with-network-security-groups"></a>Arbeta med nätverkssäkerhetsgrupper

Du kan skapa, [Visa alla](#view-all-network-security-groups), [Visa information om](#view-details-of-a-network-security-group), [ändra](#change-a-network-security-group)och [ta bort](#delete-a-network-security-group) en nätverks säkerhets grupp. Du kan också [Koppla eller koppla bort](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) en nätverks säkerhets grupp från ett nätverks gränssnitt eller ett undernät.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Det finns en gräns för hur många nätverks säkerhets grupper du kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Välj **nätverk**och välj sedan **nätverks säkerhets grupp**.
3. Ange ett **namn** för nätverks säkerhets gruppen, välj din **prenumeration**, skapa en ny **resurs grupp**eller Välj en befintlig resurs grupp, Välj en **plats**och välj sedan **skapa**.

**Kommandon**

- Azure CLI: [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Visa alla nätverks säkerhets grupper

Ange *nätverks säkerhets grupper*i rutan Sök högst upp i portalen. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den. De nätverks säkerhets grupper som finns i din prenumeration visas.

**Kommandon**

- Azure CLI: [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Visa information om en nätverks säkerhets grupp

1. Ange *nätverks säkerhets grupper*i rutan Sök högst upp i portalen. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
2. Välj den nätverks säkerhets grupp i listan som du vill visa information om. Under **Inställningar** kan du Visa **inkommande säkerhets regler** och **utgående säkerhets regler**, de **nätverks gränssnitt** och **undernät** som nätverks säkerhets gruppen är kopplad till. Du kan också aktivera eller inaktivera **diagnostikloggar** och visa **effektiva säkerhets regler**. Läs mer i [diagnostikloggar](virtual-network-nsg-manage-log.md) och [Visa effektiva säkerhets regler](diagnose-network-traffic-filter-problem.md).
3. Mer information om de vanliga Azure-inställningarna i listan finns i följande artiklar:
    *   [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md)
    *   [Åtkomst kontroll (IAM)](../role-based-access-control/overview.md)
    *   [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Hålls](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation-skript](../azure-resource-manager/templates/export-template-portal.md)

**Kommandon**

- Azure CLI: [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Ändra en nätverks säkerhets grupp

1. I rutan Sök högst upp i portalen anger du *nätverks säkerhets grupper* i sökrutan. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
2. Välj den nätverks säkerhets grupp som du vill ändra. De vanligaste ändringarna [lägger till](#create-a-security-rule) eller [tar bort](#delete-a-security-rule) säkerhets regler och [kopplar eller tar bort en nätverks säkerhets grupp till eller från ett undernät eller nätverks gränssnitt](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Kommandon**

- Azure CLI: [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Koppla eller koppla bort en nätverks säkerhets grupp till eller från ett undernät eller nätverks gränssnitt

Om du vill koppla en nätverks säkerhets grupp till eller koppla bort en nätverks säkerhets grupp från ett nätverks gränssnitt, se [associera en nätverks säkerhets grupp till eller koppla bort en nätverks säkerhets grupp från ett nätverks gränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Om du vill koppla en nätverks säkerhets grupp till eller koppla bort en nätverks säkerhets grupp från ett undernät, se [ändra under näts inställningar](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ta bort en nätverks säkerhets grupp

Om en nätverks säkerhets grupp är kopplad till undernät eller nätverks gränssnitt kan du inte ta bort den. Koppla bort en nätverks säkerhets grupp från alla undernät och nätverks gränssnitt innan du försöker ta bort den.

1. I rutan Sök högst upp i portalen anger du *nätverks säkerhets grupper* i sökrutan. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
2. Välj den nätverks säkerhets grupp som du vill ta bort från listan.
3. Välj **Ta bort** och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Arbeta med säkerhets regler

En nätverks säkerhets grupp innehåller noll eller flera säkerhets regler. Du kan skapa, [Visa alla](#view-all-security-rules), [Visa information om](#view-details-of-a-security-rule), [ändra](#change-a-security-rule)och [ta bort](#delete-a-security-rule) en säkerhets regel.

### <a name="create-a-security-rule"></a>Skapa en säkerhets regel

Det finns en gräns för hur många regler per nätverks säkerhets grupp som kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I rutan Sök högst upp i portalen anger du *nätverks säkerhets grupper* i sökrutan. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
2. Välj den nätverks säkerhets grupp i listan som du vill lägga till en säkerhets regel i.
3. Välj **inkommande säkerhets regler** under **Inställningar**. Flera befintliga regler visas. Några av reglerna som du kanske inte har lagt till. När en nätverks säkerhets grupp skapas, skapas flera standard säkerhets regler i den. Mer information finns i [Standard säkerhets regler](security-overview.md#default-security-rules).  Du kan inte ta bort standard säkerhets regler, men du kan åsidosätta dem med regler som har högre prioritet.
4. <a name = "security-rule-settings"></a>Välj **+ Lägg till**.  Välj eller Lägg till värden för följande inställningar och välj sedan **OK**:
    
    |Inställning  |Värde  |Information  |
    |---------|---------|---------|
    |Källa     | Välj **valfri**, **program säkerhets grupp**, **IP-adresser**eller **service tag** för inkommande säkerhets regler. Om du skapar en utgående säkerhets regel är alternativen samma som alternativ som anges för **mål**.       | Om du väljer **program säkerhets grupp**väljer du en eller flera befintliga program säkerhets grupper som finns i samma region som nätverks gränssnittet. Lär dig hur du [skapar en program säkerhets grupp](#create-an-application-security-group). Om du väljer **program säkerhets grupp** för både **källa** och **mål**måste nätverks gränssnitten i båda program säkerhets grupperna finnas i samma virtuella nätverk. Om du väljer **IP-adresser**anger du **Källans IP-adresser/CIDR-intervall**. Du kan ange ett enda värde eller en kommaavgränsad lista med flera värden. Ett exempel på flera värden är 10.0.0.0/16, 192.188.1.1. Det finns gränser för antalet värden som du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Om du väljer **service tag**väljer du en service tag. En service tag är en fördefinierad identifierare för en kategori med IP-adresser. Om du vill veta mer om tillgängliga Service märken och hur varje tagg representerar, se [service Taggar](security-overview.md#service-tags). Om den IP-adress du anger är tilldelad till en virtuell Azure-dator måste du se till att du anger den privata IP-adressen, inte den offentliga IP-adress som tilldelats den virtuella datorn. Säkerhets regler bearbetas när Azure översätter den offentliga IP-adressen till en privat IP-adress för inkommande säkerhets regler och innan Azure översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-diagramtyper](virtual-network-ip-addresses-overview-arm.md).        |
    |Källportintervall     | Ange en enskild port, till exempel 80, ett port intervall, till exempel 1024-65535 eller en kommaavgränsad lista med enskilda portar och/eller port intervall, till exempel 80, 1024-65535. Ange en asterisk för att tillåta trafik på vilken port som helst. | Portarna och intervallen anger vilka portar trafik tillåts eller nekas av regeln. Det finns gränser för antalet portar som du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .  |
    |Mål     | Välj **valfri**, **program säkerhets grupp**, **IP-adresser**eller **Virtual Network** för utgående säkerhets regler. Om du skapar en inkommande säkerhets regel är alternativen samma som alternativ som anges för **källa**.        | Om du väljer **program säkerhets grupp** måste du sedan välja en eller flera befintliga program säkerhets grupper som finns i samma region som nätverks gränssnittet. Lär dig hur du [skapar en program säkerhets grupp](#create-an-application-security-group). Om du väljer **program säkerhets grupp**väljer du en befintlig program säkerhets grupp som finns i samma region som nätverks gränssnittet. Om du väljer **IP-adresser**anger du **målets IP-adresser/CIDR-intervall**. På samma sätt som **käll** **-och käll-IP-adresser/CIDR-intervall**kan du ange en eller flera adresser eller intervall, och det finns gränser för antalet som du kan ange. Om du väljer **virtuellt nätverk**, vilket är en tjänst tag, innebär det att trafiken tillåts för alla IP-adresser inom det virtuella nätverkets adress utrymme. Om den IP-adress du anger är tilldelad till en virtuell Azure-dator måste du se till att du anger den privata IP-adressen, inte den offentliga IP-adress som tilldelats den virtuella datorn. Säkerhets regler bearbetas när Azure översätter den offentliga IP-adressen till en privat IP-adress för inkommande säkerhets regler och innan Azure översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-diagramtyper](virtual-network-ip-addresses-overview-arm.md).        |
    |Målportintervall     | Ange ett enskilt värde eller en kommaavgränsad lista med värden. | På samma sätt som **käll ports intervall**kan du ange en eller flera portar och intervall, och det finns gränser för antalet som du kan ange. |
    |Protokoll     | Välj **valfri**, **TCP**, **UDP** eller **ICMP**.        |         |
    |Åtgärd     | Välj **Tillåt** eller **neka**.        |         |
    |Prioritet     | Ange ett värde mellan 100-4096 som är unikt för alla säkerhets regler i nätverks säkerhets gruppen. |Regler bearbetas i prioritetsordning. Ju lägre siffra, desto högre prioritet. Vi rekommenderar att du lämnar ett mellanrum mellan prioritets nummer när du skapar regler, till exempel 100, 200, 300. Om du lämnar luckor blir det enklare att lägga till regler i framtiden som du kan behöva göra högre eller lägre än befintliga regler.         |
    |Namn     | Ett unikt namn för regeln inom nätverks säkerhets gruppen.        |  Namnet kan bestå av upp till 80 tecken. Det måste börja med en bokstav eller en siffra, sluta med en bokstav, en siffra eller ett under streck och får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck.       |
    |Beskrivning     | En valfri beskrivning.        |         |

**Kommandon**

- Azure CLI: [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Visa alla säkerhets regler

En nätverks säkerhets grupp innehåller noll eller flera regler. Mer information om den information som visas när du visar regler finns i [Översikt över nätverks säkerhets grupper](security-overview.md).

1. Ange *nätverks säkerhets grupper*i rutan Sök högst upp i portalen. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
2. Välj den nätverks säkerhets grupp i listan som du vill visa regler för.
3. Välj **inkommande säkerhets regler** eller **utgående säkerhets regler** under **Inställningar**.

Listan innehåller alla regler som du har skapat och nätverks säkerhets gruppens [Standard säkerhets regler](security-overview.md#default-security-rules).

**Kommandon**

- Azure CLI: [AZ Network NSG Rule List](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

1. Ange *nätverks säkerhets grupper*i rutan Sök högst upp i portalen. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
2. Välj den nätverks säkerhets grupp som du vill visa information om en säkerhets regel för.
3. Välj **inkommande säkerhets regler** eller **utgående säkerhets regler** under **Inställningar**.
4. Välj den regel som du vill visa information om. En detaljerad förklaring av alla inställningar finns i [säkerhets regel inställningar](#security-rule-settings).

**Kommandon**

- Azure CLI: [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Ändra en säkerhets regel

1. Slutför stegen i [Visa information om en säkerhets regel](#view-details-of-a-security-rule).
2. Ändra inställningarna efter behov och välj sedan **Spara**. En detaljerad förklaring av alla inställningar finns i [säkerhets regel inställningar](#security-rule-settings).

**Kommandon**

- Azure CLI: [AZ Network NSG Rule Update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Ta bort en säkerhets regel

1. Slutför stegen i [Visa information om en säkerhets regel](#view-details-of-a-security-rule).
2. Välj **Ta bort** och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [AZ Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Arbeta med programsäkerhetsgrupper

En program säkerhets grupp innehåller noll eller flera nätverks gränssnitt. Mer information finns i [program säkerhets grupper](security-overview.md#application-security-groups). Alla nätverks gränssnitt i en program säkerhets grupp måste finnas i samma virtuella nätverk. Information om hur du lägger till ett nätverks gränssnitt i en program säkerhets grupp finns i [lägga till ett nätverks gränssnitt i en program säkerhets grupp](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Skapa en program säkerhets grupp

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. I rutan **Sök på Marketplace** anger du *Programsäkerhetsgrupp*. När **programsäkerhetsgruppen** visas i sökresultatet väljer du den. Välj sedan **Programsäkerhetsgrupp** igen under **Allt** och välj sedan **Skapa**.
3. Välj eller ange följande information och välj **Skapa**:

    | Inställning        | Värde                                                   |
    | ---            | ---                                                     |
    | Namn           | Namnet måste vara unikt inom sin resursgrupp.        |
    | Prenumeration   | Välj din prenumeration.                               |
    | Resursgrupp | Välj en befintlig resurs grupp eller skapa en ny. |
    | Location       | Välja en plats                                       |

**Kommandon**

- Azure CLI: [AZ Network grupperna Create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Visa alla program säkerhets grupper

1. Välj **alla tjänster** i det övre, vänstra hörnet av Azure Portal.
2. Ange *program säkerhets grupper* i rutan **alla tjänster filter** och välj sedan **program säkerhets grupper** när de visas i Sök resultaten.

**Kommandon**

- Azure CLI: [AZ Network grupperna List](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Visa information om en specifik program säkerhets grupp

1. Välj **alla tjänster** i det övre, vänstra hörnet av Azure Portal.
2. Ange *program säkerhets grupper* i rutan **alla tjänster filter** och välj sedan **program säkerhets grupper** när de visas i Sök resultaten.
3. Välj den program säkerhets grupp som du vill visa information om.

**Kommandon**

- Azure CLI: [AZ Network grupperna show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Ändra en program säkerhets grupp

1. Välj **alla tjänster** i det övre, vänstra hörnet av Azure Portal.
2. Ange *program säkerhets grupper* i rutan **alla tjänster filter** och välj sedan **program säkerhets grupper** när de visas i Sök resultaten.
3. Välj den program säkerhets grupp som du vill ändra inställningarna för. Du kan lägga till eller ta bort taggar eller tilldela eller ta bort behörigheter till program säkerhets gruppen.

- Azure CLI: [AZ Network grupperna Update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: ingen PowerShell-cmdlet.

### <a name="delete-an-application-security-group"></a>Ta bort en program säkerhets grupp

Du kan inte ta bort en program säkerhets grupp om den har nätverks gränssnitt. Ta bort alla nätverks gränssnitt från program säkerhets gruppen genom att antingen ändra inställningarna för nätverks gränssnittet eller ta bort nätverks gränssnitten. Mer information finns i [lägga till eller ta bort ett nätverks gränssnitt från program säkerhets grupper](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) eller [ta bort ett nätverks gränssnitt](virtual-network-network-interface.md#delete-a-network-interface).

1. Välj **alla tjänster** i det övre, vänstra hörnet av Azure Portal.
2. Ange *program säkerhets grupper* i rutan **alla tjänster filter** och välj sedan **program säkerhets grupper** när de visas i Sök resultaten.
3. Välj den program säkerhets grupp som du vill ta bort.
4. Välj **ta bort**och välj sedan **Ja** för att ta bort program säkerhets gruppen.

**Kommandon**

- Azure CLI: [AZ Network grupperna Delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Behörigheter

För att utföra uppgifter om nätverks säkerhets grupper, säkerhets regler och program säkerhets grupper måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga behörigheter i följande tabeller:

### <a name="network-security-group"></a>Nätverkssäkerhetsgrupp

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hämta nätverks säkerhets grupp                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Skapa eller uppdatera nätverks säkerhets grupp                             |
| Microsoft. Network/networkSecurityGroups/Delete                |   Ta bort nätverks säkerhets grupp                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Koppla en nätverks säkerhets grupp till ett undernät eller ett nätverks gränssnitt 


### <a name="network-security-group-rule"></a>Regel för nätverks säkerhets grupp

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/Rules/Read            |   Hämta regel                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Skapa eller uppdatera regel                                               |
| Microsoft. Network/networkSecurityGroups/Rules/Delete          |   Ta bort regel                                                         |

### <a name="application-security-group"></a>Program säkerhets grupp

| Åtgärd                                                                     | Namn                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action     | Koppla en IP-konfiguration till en program säkerhets grupp|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Koppla en säkerhets regel till en program säkerhets grupp    |
| Microsoft. Network/applicationSecurityGroups/Read                           | Hämta en program säkerhets grupp                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Skapa eller uppdatera en program säkerhets grupp           |
| Microsoft. Network/applicationSecurityGroups/Delete                         | Ta bort en program säkerhets grupp                     |

## <a name="next-steps"></a>Nästa steg

- Skapa en nätverks-eller program säkerhets grupp med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk
