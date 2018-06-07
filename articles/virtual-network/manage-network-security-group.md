---
title: Skapa, ändra eller ta bort en Azure nätverkssäkerhetsgruppen | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort en nätverkssäkerhetsgrupp.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 22cf62f201b21f3035687b7f0f2ff07dc94f1a29
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658680"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp

Säkerhetsregler i nätverkssäkerhetsgrupper kan du filtrera typ av nätverkstrafik kan flöda till och från virtuella undernät och nätverksgränssnitt. Om du inte är bekant med nätverkssäkerhetsgrupper [nätverk Säkerhetsöversikt grupp](security-overview.md) Lär dig mer om dem och genomföra den [filtrera nätverkstrafik](tutorial-filter-network-traffic.md) kursen hjälper dig att få viss erfarenhet av nätverk säkerhetsgrupper.

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

Kontot du loggar in, eller Anslut till Azure med måste tilldelas den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="work-with-network-security-groups"></a>Arbeta med nätverkssäkerhetsgrupper

Du kan skapa [visa alla](#view-all-network-security-groups), [Visa detaljer för](#view-details-of-a-network-security-group), [ändra](#change-a-network-security-group), och [ta bort](#delete-a-network-security-group) en nätverkssäkerhetsgrupp. Du kan också [koppla eller koppla bort](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) en nätverkssäkerhetsgrupp från nätverksgränssnittet eller ett undernät.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Det finns en gräns för hur många nätverkssäkerhetsgrupper kan du skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I det övre vänstra hörnet av portalen väljer **+ skapa en resurs för**.
2. Välj **nätverk**och välj **nätverkssäkerhetsgruppen**.
3. Ange en **namn** nätverkssäkerhetsgruppen, Välj din **prenumeration**, skapa en ny **resursgruppen**, eller välj en befintlig resursgrupp, Välj en **Plats**, och välj sedan **skapa**.

**Kommandon**

- Azure CLI: [az nätverket nsg skapa](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [nya AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Visa alla nätverkssäkerhetsgrupper

Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den. Nätverkssäkerhetsgrupper som finns i din prenumeration visas.

**Kommandon**

- Azure CLI: [az nätverkslistan nsg](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Visa information om en nätverkssäkerhetsgrupp

1. Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj nätverkssäkerhetsgruppen i listan som du vill visa information om. Under **inställningar** du kan visa den **inkommande säkerhetsregler** och **utgående säkerhetsregler**, **nätverksgränssnitt** och  **Undernät** nätverkssäkerhetsgruppen som är kopplad till. Du kan också aktivera eller inaktivera **diagnostikloggar** och visa **effektiva säkerhetsregler**. Läs mer i [diagnostikloggar](virtual-network-nsg-manage-log.md) och [visa effektiva säkerhetsregler](diagnose-network-traffic-filter-problem.md).
3. Mer information om vanliga Azure inställningarna finns i följande artiklar:
    *   [Aktivitetslogg](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Åtkomstkontroll (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation-skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Kommandon**

- Azure CLI: [az nätverket nsg visa](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Ändra en nätverkssäkerhetsgrupp

1. Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper* i sökrutan. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj nätverkssäkerhetsgrupp som du vill ändra. De vanligaste förändringar är [att lägga till](#create-a-security-rule) eller [tar bort](#delete-a-security-rule) säkerhetsregler och [Associating eller dissociating en nätverkssäkerhetsgrupp till eller från ett undernät eller nätverk gränssnitt](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Kommandon**

- Azure CLI: [az nätverket nsg uppdatering](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Koppla eller koppla bort en nätverkssäkerhetsgrupp till eller från ett undernät eller nätverk gränssnitt

Om du vill koppla en nätverkssäkerhetsgrupp till eller koppla bort en nätverkssäkerhetsgrupp från ett nätverksgränssnitt, se [associera en nätverkssäkerhetsgrupp till eller koppla bort en nätverkssäkerhetsgrupp från ett nätverksgränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Om du vill koppla en nätverkssäkerhetsgrupp till eller koppla bort en nätverkssäkerhetsgrupp från ett undernät, se [ändra undernätsinställningar](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ta bort en nätverkssäkerhetsgrupp

Om en säkerhetsgrupp för nätverk är kopplat till undernät eller nätverksgränssnitt kan inte tas bort. [Koppla bort](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) en nätverkssäkerhetsgrupp från alla undernät och nätverksgränssnitt innan du försöker ta bort den.

1. Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper* i sökrutan. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj nätverkssäkerhetsgrupp som du vill ta bort från listan.
3. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az nätverket nsg ta bort](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [ta bort AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Arbeta med säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller flera säkerhetsregler. Du kan skapa [visa alla](#view-all-security-rules), [Visa detaljer för](#view-details-of-a-security-rule), [ändra](#change-a-security-rule), och [ta bort](#delete-a-security-rule) en säkerhetsregel.

### <a name="create-a-security-rule"></a>Skapa en säkerhetsregel

Det finns en gräns för hur många regler per nätverkssäkerhetsgruppen kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper* i sökrutan. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj i listan som du vill lägga till en säkerhetsregel till nätverkssäkerhetsgruppen.
3. Välj **inkommande säkerhetsregler** under **inställningar**. Flera befintliga regler visas. Vissa av de regler som du inte kanske har lagt till. När en nätverkssäkerhetsgrupp skapas, skapas flera standardregler för säkerhet i den. Läs mer i [standard säkerhetsregler](security-overview.md#default-security-rules).  Du kan inte ta bort standardregler för säkerhet, men du kan åsidosätta dem med regler som har högre prioritet.
4. <a name = "security-rule-settings"></a>Välj **+ Lägg till**.  Markera eller lägga till värden för följande inställningar och välj sedan **OK**:
    
    |Inställning  |Värde  |Information  |
    |---------|---------|---------|
    |Källa     | Välj **alla**, **IP-adresser**, eller **tjänsten taggen**.        | Om du väljer **IP-adresser**, måste du ange **käll-IP-adresser/CIDR-intervallen**. Du kan ange ett enda värde eller en kommaavgränsad lista med flera värden. Ett exempel på flera värden är 10.0.0.0/16 192.188.1.1. Det finns begränsningar för antalet värden som du kan ange. Se [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) mer information. Om du väljer **Service Tag**, måste du ange en tagg för tjänsten. Taggen service är en fördefinierad identifierare för en kategori av IP-adresser. Mer information om tillgängliga taggar och varje tagg representerar finns [tjänsten taggar](security-overview.md#service-tags)        |
    |Källportintervall     | Ange en enskild port, till exempel 80, ett portintervall, till exempel 1024-65535, eller en kommaavgränsad lista med enda portar eller portintervall, till exempel 80, 1024-65535. Ange en asterisk som tillåter trafik på alla portar. | Ange vilka portar trafik som tillåts eller nekas av regeln portar och adressintervall. Det finns begränsningar för antalet portar som du kan ange. Se [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) mer information.  |
    |Mål     | Välj **alla**, **IP-adresser**, eller **virtuellt nätverk**.        | Om du väljer **IP-adresser**, måste du ange **mål-IP-adresser/CIDR-intervallen**. Liknar **källa** och **käll-IP-adresser/CIDR-intervallen**, du kan ange en enda eller flera adresser eller ett intervall och det finns begränsningar för antalet du kan ange. Att välja **för virtuella nätverk**, vilket är en tjänst-tagg, innebär det att trafik tillåts alla IP-adresser inom adressutrymmet för det virtuella nätverket.        |
    |Målportintervall     | Ange ett värde eller en kommaavgränsad lista med värden. | Liknar **datakällan portintervall**, kan du ange en enda eller flera portar och adressintervall och det finns begränsningar för antalet du kan ange. |
    |Protokoll     | Välj **alla**, **TCP**, eller **UDP**.        |         |
    |Åtgärd     | Välj **Tillåt** eller **neka**.        |         |
    |Prioritet     | Ange ett värde mellan 100-4096 som är unikt för alla säkerhetsregler inom nätverkssäkerhetsgruppen. |Regler bearbetas i prioritetsordning. Ju lägre nummer, desto högre prioritet. Vi rekommenderar att du behåller en lucka mellan prioritet tal när du skapar regler, till exempel 100, 200, 300. Lämnar mellanrum gör det enklare att lägga till regler i framtiden som du kan behöva göra högre eller lägre än befintliga regler.         |
    |Namn     | Ett unikt namn för regeln inom nätverkssäkerhetsgruppen.        |  Namnet kan vara upp till 80 tecken. Det måste börja med en bokstav eller siffra, sluta med en bokstav, siffra eller understreck och får innehålla endast bokstäver, siffror, understreck, punkter eller bindestreck.       |
    |Beskrivning     | En valfri beskrivning.        |         |

    Du kan inte ange en [programmet säkerhetsgrupp](#work-with-application-security-groups) för den **källa** eller **mål** inställningar med hjälp av portalen. Du kan dock använda Azure CLI eller PowerShell. Inställningarna för **utgående säkerhetsregler** liknar varandra, så att de inte omfattas separat.

**Kommandon**

- Azure CLI: [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [nya AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Visa alla säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller flera regler. Mer information om den information som visas när du visar regler finns [nätverk Säkerhetsöversikt för gruppen](security-overview.md).

1. Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj i listan som du vill visa regler för nätverkssäkerhetsgruppen.
3. Välj **inkommande säkerhetsregler** eller **utgående säkerhetsregler** under **inställningar**.

Listan innehåller de regler som du har skapat och nätverkssäkerhetsgruppen [standard säkerhetsregler](security-overview.md#default-security-rules).

**Kommandon**

- Azure CLI: [az nätverkslistan nsg regel](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

1. Skriv i sökrutan överst i portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj nätverkssäkerhetsgrupp som du vill visa information om en säkerhetsregel för.
3. Välj **inkommande säkerhetsregler** eller **utgående säkerhetsregler** under **inställningar**.
4. Välj en regel som du vill visa information om. En detaljerad förklaring av alla inställningar finns i [regeln säkerhetsinställningar](#security-rule-settings).

**Kommandon**

- Azure CLI: [az nätverket nsg regeln visa](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Ändra en säkerhetsregel

1. Utför stegen i [Visa detaljer för en säkerhetsregel](#view-details-of-a-security-rule).
2. Ändra inställningarna efter behov och väljer sedan **spara**. En detaljerad förklaring av alla inställningar finns i [regeln säkerhetsinställningar](#security-rule-settings).

**Kommandon**

- Azure CLI: [az uppdatera regel för nätverket nsg](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Ta bort en säkerhetsregel

1. Utför stegen i [Visa detaljer för en säkerhetsregel](#view-details-of-a-security-rule).
2. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az datornätverket nsg regeln ta bort](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [ta bort AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>Arbeta med säkerhetsgrupper för programmet

En säkerhetsgrupp i programmet innehåller noll eller fler nätverksgränssnitt. Läs mer i [programmet säkerhetsgrupper](security-overview.md#application-security-groups). Du kan inte arbeta med program säkerhetsgrupper i portalen, men du kan använda PowerShell eller Azure CLI. Alla nätverksgränssnitt i en säkerhetsgrupp för programmet måste finnas i samma virtuella nätverk. Första nätverksgränssnittet som lagts till i en säkerhetsgrupp för programmet avgör vilka virtuella nätverket alla efterföljande nätverksgränssnitt måste vara i. Information om hur du lägger till ett nätverksgränssnitt till en säkerhetsgrupp för programmet finns [lägga till ett nätverksgränssnitt i en säkerhetsgrupp för programmet](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Skapa en säkerhetsgrupp för programmet

- Azure CLI: [az nätverket asg skapa](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [nya AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Visa alla program-säkerhetsgrupper

- Azure CLI: [az asg nätverkslistan](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Visa information om en specifik säkerhetsgrupp

- Azure CLI: [az nätverket asg visa](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Ändra en säkerhetsgrupp för programmet

Medan du kan ändra vissa inställningar, till exempel taggar och behörigheter för en befintlig säkerhetsgrupp för programmet, kan du inte ändra dess namn och plats.

- Azure CLI: [az nätverket asg uppdatering](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Ingen PowerShell-cmdlet.

### <a name="delete-an-application-security-group"></a>Ta bort en säkerhetsgrupp för programmet

Du kan inte ta bort en säkerhetsgrupp för programmet om den har alla nätverksgränssnitt i den. Ta bort alla nätverksgränssnitt från säkerhetsgruppen program genom att antingen ändra inställningar för nätverksgränssnittet eller ta bort nätverksgränssnitten. Mer information finns i [Lägg till eller ta bort ett nätverksgränssnitt från programmet säkerhetsgrupper](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) eller [ta bort ett nätverksgränssnitt](virtual-network-network-interface.md#delete-a-network-interface).

**Kommandon**

- Azure CLI: [az nätverket asg ta bort](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [ta bort AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Behörigheter

För att utföra uppgifter på nätverkssäkerhetsgrupper säkerhetsregler och säkerhetsgrupper för program, ditt konto måste ha tilldelats den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som är tilldelade den behörighet som visas i följande tabeller:

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/ruleTables/read                             |   Hämta nätverkssäkerhetsgrupp                                          |
| Microsoft.Network/ruleTables/write                            |   Skapa eller uppdatera nätverkssäkerhetsgrupp                             |
| Microsoft.Network/ruleTables/delete                           |   Ta bort nätverkssäkerhetsgruppen                                       |
| Microsoft.Network/ruleTables/join/action                      |   Associera en säkerhetsgrupp för nätverk till ett undernät eller nätverk gränssnitt |
| Microsoft.Network/ruleTables/rules/read                       |   Hämta regel                                                            |
| Microsoft.Network/ruleTables/rules/write                      |   Skapa eller uppdatera regel                                               |
| Microsoft.Network/ruleTables/rules/delete                     |   Ta bort regeln                                                         |
| Microsoft.Network/networkInterfaces/effectiveruleTable/action |   Hämta Network Interface effektiva nätverkssäkerhetsgrupp              |
| Microsoft.Network/networkWatchers/nextHop/action              |   Hämtar nästa hopp från en virtuell dator                                         |

### <a name="application-security-groups"></a>Programsäkerhetsgrupper

| Åtgärd                                                                     | Namn                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Ansluta till IP-konfiguration till en säkerhetsgrupp för programmet|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Anslut en säkerhetsregel till en säkerhetsgrupp för programmet    |
| Microsoft.Network/applicationSecurityGroups/read                           | Hämta en säkerhetsgrupp för programmet                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Skapa eller uppdatera en säkerhetsgrupp för programmet           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Ta bort en säkerhetsgrupp för programmet                     |

## <a name="next-steps"></a>Nästa steg

- Skapa ett nätverk eller program säkerhet med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och använda [Azure princip](policy-samples.md) för virtuella nätverk