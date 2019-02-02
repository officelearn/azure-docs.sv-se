---
title: Skapa, ändra eller ta bort en säkerhetsgrupp i Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig mer om att skapa, ändra eller ta bort en grupp.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 28b73a70ef9ffd4e73117d599448434823747eca
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567408"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Skapa, ändra eller ta bort en grupp

Säkerhetsregler i nätverkssäkerhetsgrupper kan du filtrera typ av nätverkstrafik som kan flöda och från undernät i virtuella nätverk och nätverksgränssnitt. Om du inte är bekant med nätverkssäkerhetsgrupper [översikt över Network security group](security-overview.md) vill veta mer om dem och slutföra den [filtrera nätverkstrafik](tutorial-filter-network-traffic.md) guide för att få lite erfarenhet av nätverk säkerhetsgrupper.

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-Modulversion 5.4.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="work-with-network-security-groups"></a>Arbeta med nätverkssäkerhetsgrupper

Du kan skapa [visa alla](#view-all-network-security-groups), [visa information om](#view-details-of-a-network-security-group), [ändra](#change-a-network-security-group), och [ta bort](#delete-a-network-security-group) en nätverkssäkerhetsgrupp. Du kan också [koppla eller koppla bort](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) en grupp från en nätverksgränssnitt eller undernät.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Det finns en gräns för hur många nätverkssäkerhetsgrupper du kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I det övre vänstra hörnet i portalen väljer du **+ skapa en resurs**.
2. Välj **nätverk**och välj sedan **nätverkssäkerhetsgrupp**.
3. Ange en **namn** nätverkssäkerhetsgruppen, Välj din **prenumeration**, skapa en ny **resursgrupp**, eller välj en befintlig resursgrupp, Välj en **Plats**, och välj sedan **skapa**.

**Kommandon**

- Azure CLI: [skapa az network nsg](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Visa alla nätverkssäkerhetsgrupper

Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den. Nätverkssäkerhetsgrupper som finns i din prenumeration visas.

**Kommandon**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Visa information om en nätverkssäkerhetsgrupp

1. Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj den nya nätverkssäkerhetsgruppen i listan som du vill visa information om. Under **inställningar** du kan visa den **ingående säkerhetsregler** och **utgående säkerhetsregler**, **nätverksgränssnitt** och  **Undernät** nätverkssäkerhetsgruppen är kopplad till. Du kan också aktivera eller inaktivera **diagnostikloggar** och visa **gällande säkerhetsregler**. Mer information finns i [diagnostikloggar](virtual-network-nsg-manage-log.md) och [visa gällande säkerhetsregler](diagnose-network-traffic-filter-problem.md).
3. Mer information om vanliga Azure inställningarna finns i följande artiklar:
    *   [Aktivitetslogg](../azure-monitor/platform/activity-logs-overview.md)
    *   [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
    *   [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automationsskript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Kommandon**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Ändra en grupp

1. Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper* i sökrutan. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj den grupp du vill ändra. De vanligaste förändringar är [att lägga till](#create-a-security-rule) eller [tar bort](#delete-a-security-rule) säkerhetsregler och [Associating eller kopplar bort en nätverkssäkerhetsgrupp till eller från ett undernät eller ett gränssnitt](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Kommandon**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Koppla eller koppla bort en nätverkssäkerhetsgrupp till eller från ett undernät eller ett gränssnitt

Om du vill associera en nätverkssäkerhetsgrupp till eller koppla bort en grupp från ett nätverksgränssnitt, se [associerar en nätverkssäkerhetsgrupp till eller koppla bort en grupp från ett nätverksgränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Om du vill associera en nätverkssäkerhetsgrupp till eller koppla bort en grupp från ett undernät, se [ändra undernätsinställningar](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ta bort en grupp

Om en nätverkssäkerhetsgrupp är associerad till alla undernät eller ett nätverksgränssnitt, kan inte tas bort. [Koppla bort](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) en nätverkssäkerhetsgrupp från alla undernät och nätverksgränssnitt innan du försöker ta bort den.

1. Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper* i sökrutan. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj den grupp du vill ta bort från listan.
3. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az network nsg ta bort](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroup) 

## <a name="work-with-security-rules"></a>Arbeta med säkerhetsregler

En nätverkssäkerhetsgrupp innehåller säkerhetsregler noll eller flera. Du kan skapa [visa alla](#view-all-security-rules), [visa information om](#view-details-of-a-security-rule), [ändra](#change-a-security-rule), och [ta bort](#delete-a-security-rule) en säkerhetsregel.

### <a name="create-a-security-rule"></a>Skapa en säkerhetsregel

Det finns en gräns för hur många regler per nätverkssäkerhetsgrupp kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper* i sökrutan. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj i listan som du vill lägga till en säkerhetsregel till nätverkssäkerhetsgruppen.
3. Välj **ingående säkerhetsregler** under **inställningar**. Flera befintliga regler visas. Några av de regler som du inte kanske har lagt till. När en nätverkssäkerhetsgrupp har skapats skapas flera standardsäkerhetsregler i den. Mer information finns i [standardsäkerhetsregler](security-overview.md#default-security-rules).  Du kan inte ta bort standardsäkerhetsregler, men du kan åsidosätta dem med regler som har högre prioritet.
4. <a name = "security-rule-settings"></a>Välj **+ Lägg till**.  Välj eller Lägg till värden för följande inställningar och välj sedan **OK**:
    
    |Inställning  |Värde  |Information  |
    |---------|---------|---------|
    |Källa     | Välj **alla**, **programsäkerhetsgruppen**, **IP-adresser**, eller **Tjänsttagg** för inkommande säkerhetsregler. Om du skapar en utgående säkerhetsregel, är alternativen samma som alternativen för **mål**.       | Om du väljer **programsäkerhetsgruppen**, välj sedan en eller flera befintliga programsäkerhetsgrupper som finns i samma region som nätverksgränssnittet. Lär dig hur du [skapa en programsäkerhetsgrupp](#create-an-application-security-group). Om du väljer **programsäkerhetsgruppen** för både den **källa** och **mål**, nätverksgränssnitten i bägge måste vara i samma virtuellt nätverk. Om du väljer **IP-adresser**, ange **käll-IP-adresser/CIDR-intervall**. Du kan ange ett enda värde eller en kommaavgränsad lista med flera värden. Ett exempel på flera värden är 10.0.0.0/16, 192.188.1.1. Det finns gränser för antalet värden som du kan ange. Se [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) mer information. Om du väljer **Tjänsttagg**, välj sedan en tjänsttagg. En tjänsttagg är en fördefinierad identifierare för en viss kategori av IP-adresser. Läs mer om tillgängliga tjänsttaggar och vad varje tagg representerar i [Tjänsttaggar](security-overview.md#service-tags). Om IP-adress som du anger tilldelas till en Azure virtuell dator, kan du kontrollera att du anger den privata IP-Adressen, inte den offentliga IP-adressen till den virtuella datorn. Säkerhetsregler bearbetas när Azure översätter offentliga IP-adress till en privat IP-adress för inkommande säkerhetsregler och innan Azure omvandlar en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns [IP-adresstyper](virtual-network-ip-addresses-overview-arm.md).        |
    |Källportintervall     | Ange en enskild port, till exempel 80, ett portintervall, till exempel 1024-65535, eller en kommaavgränsad lista över enskilda portar och/eller portintervall, till exempel 80, 1024-65535. Ange en asterisk för att tillåta trafik på alla portar. | Ange vilka portar trafik tillåts eller nekas av regeln portar och adressintervall. Det finns gränser för antalet portar som du kan ange. Se [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) mer information.  |
    |Mål     | Välj **alla**, **programsäkerhetsgruppen**, **IP-adresser**, eller **virtuellt nätverk** för inkommande säkerhetsregler. Om du skapar en utgående säkerhetsregel, är alternativen samma som alternativen för **källa**.        | Om du väljer **programsäkerhetsgruppen** sedan måste du välja en eller flera befintliga programsäkerhetsgrupper som finns i samma region som nätverksgränssnittet. Lär dig hur du [skapa en programsäkerhetsgrupp](#create-an-application-security-group). Om du väljer **programsäkerhetsgruppen**, Välj en befintlig programsäkerhetsgrupp som finns i samma region som nätverksgränssnittet. Om du väljer **IP-adresser**, ange **målets IP-adresser/CIDR-intervall**. Liknar **källa** och **käll-IP-adresser/CIDR-intervall**, du kan ange en enda eller flera adresser eller intervall och det finns begränsningar för hur många som du kan ange. Att välja **virtuellt nätverk**, vilket är en tjänsttagg, innebär det att trafik tillåts till alla IP-adresser inom adressutrymmet för det virtuella nätverket. Om IP-adress som du anger tilldelas till en Azure virtuell dator, kan du kontrollera att du anger den privata IP-Adressen, inte den offentliga IP-adressen till den virtuella datorn. Säkerhetsregler bearbetas när Azure översätter offentliga IP-adress till en privat IP-adress för inkommande säkerhetsregler och innan Azure omvandlar en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns [IP-adresstyper](virtual-network-ip-addresses-overview-arm.md).        |
    |Målportintervall     | Ange ett enstaka värde eller en kommaavgränsad lista med värden. | Liknar **Source portintervall**, du kan ange en enda eller flera portar och adressintervall och det finns begränsningar för hur många som du kan ange. |
    |Protokoll     | Välj **alla**, **TCP**, eller **UDP**.        |         |
    |Åtgärd     | Välj **Tillåt** eller **neka**.        |         |
    |Prioritet     | Ange ett värde mellan 100-4096 som är unikt för alla säkerhetsregler i nätverkssäkerhetsgruppen. |Regler bearbetas i prioritetsordning. Ju lägre nummer, desto högre prioritet. Vi rekommenderar att du lämnar en lucka mellan prioritet tal när du skapar regler, till exempel 100, 200, 300. Blir det luckor i gör det enklare att lägga till regler i framtiden som du kan behöva göra högre eller lägre än befintliga regler.         |
    |Namn     | Ett unikt namn för regeln i nätverkssäkerhetsgruppen.        |  Namnet kan vara upp till 80 tecken. Det måste börja med en bokstav eller siffra, sluta med en bokstav, siffra eller understreck och får innehålla endast bokstäver, siffror, understreck, punkter eller bindestreck.       |
    |Beskrivning     | En valfri beskrivning.        |         |

**Kommandon**

- Azure CLI: [skapa az network nsg-regel](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Visa alla säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller flera regler. Mer information om vilken information som visas när du visar regler finns [översikt över Network security group](security-overview.md).

1. Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj i listan som du vill visa regler för nätverkssäkerhetsgruppen.
3. Välj **ingående säkerhetsregler** eller **utgående säkerhetsregler** under **inställningar**.

Listan innehåller alla regler som du har skapat och nätverkssäkerhetsgruppen [standardsäkerhetsregler](security-overview.md#default-security-rules).

**Kommandon**

- Azure CLI: [az network nsg rule lista](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

1. Ange i sökrutan överst på portalen *nätverkssäkerhetsgrupper*. När **nätverkssäkerhetsgrupper** visas i sökresultaten, markerar du den.
2. Välj den grupp du vill visa information om en säkerhetsregel för.
3. Välj **ingående säkerhetsregler** eller **utgående säkerhetsregler** under **inställningar**.
4. Välj den regel som du vill visa information om. En detaljerad förklaring av alla inställningar finns i [säkerhetsinställningar för regeln](#security-rule-settings).

**Kommandon**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Ändra en säkerhetsregel

1. Utför stegen i [visa information om en säkerhetsregel](#view-details-of-a-security-rule).
2. Ändra inställningarna efter behov och sedan väljer **spara**. En detaljerad förklaring av alla inställningar finns i [säkerhetsinställningar för regeln](#security-rule-settings).

**Kommandon**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Ta bort en säkerhetsregel

1. Utför stegen i [visa information om en säkerhetsregel](#view-details-of-a-security-rule).
2. Välj **ta bort**, och välj sedan **Ja**.

**Kommandon**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Arbeta med programsäkerhetsgrupper

En programsäkerhetsgrupp innehåller noll eller flera nätverksgränssnitt. Mer information finns i [programsäkerhetsgrupper](security-overview.md#application-security-groups). Alla nätverksgränssnitt i en programsäkerhetsgrupp måste finnas i samma virtuella nätverk. Läs hur du lägger till ett nätverksgränssnitt till en programsäkerhetsgrupp i [lägga till ett nätverksgränssnitt i en programsäkerhetsgrupp](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Skapa en programsäkerhetsgrupp

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. I rutan **Sök på Marketplace** anger du *Programsäkerhetsgrupp*. När **programsäkerhetsgruppen** visas i sökresultatet väljer du den. Välj sedan **Programsäkerhetsgrupp** igen under **Allt** och välj sedan **Skapa**.
3. Välj eller ange följande information och välj **Skapa**:

    | Inställning        | Värde                                                   |
    | ---            | ---                                                     |
    | Namn           | Namnet måste vara unikt inom sin resursgrupp.        |
    | Prenumeration   | Välj din prenumeration.                               |
    | Resursgrupp | Välj en befintlig resursgrupp eller skapa en ny. |
    | Plats       | Välj en plats                                       |

**Kommandon**

- Azure CLI: [az nätverket asg skapa](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Visa alla programsäkerhetsgrupper

1. Välj **alla tjänster** i övre vänstra hörnet i Azure Portal.
2. Ange *programsäkerhetsgrupper* i den **alla tjänster Filter** och väljer sedan **programsäkerhetsgrupper** när den visas i sökresultaten.

**Kommandon**

- Azure CLI: [az nätverket asg lista](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Visa information om en specifik säkerhetsgrupp

1. Välj **alla tjänster** i övre vänstra hörnet i Azure Portal.
2. Ange *programsäkerhetsgrupper* i den **alla tjänster Filter** och väljer sedan **programsäkerhetsgrupper** när den visas i sökresultaten.
3. Välj den säkerhetsgrupp för programmet som du vill visa information om.

**Kommandon**

- Azure CLI: [az nätverket asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Ändra en programsäkerhetsgrupp

1. Välj **alla tjänster** i övre vänstra hörnet i Azure Portal.
2. Ange *programsäkerhetsgrupper* i den **alla tjänster Filter** och väljer sedan **programsäkerhetsgrupper** när den visas i sökresultaten.
3. Välj den säkerhetsgrupp för programmet som du vill ändra inställningarna för. Du kan lägga till eller ta bort taggar, eller tilldela eller ta bort behörigheter till programsäkerhetsgruppen.

- Azure CLI: [az nätverket asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Ingen PowerShell-cmdlet.

### <a name="delete-an-application-security-group"></a>Ta bort en programsäkerhetsgrupp

Du kan inte ta bort en programsäkerhetsgrupp om den har alla nätverksgränssnitt i den. Ta bort alla nätverksgränssnitt från programsäkerhetsgruppen genom att antingen ändra inställningar för nätverksgränssnittet eller ta bort nätverksgränssnitt. Mer information finns i [Lägg till eller ta bort ett nätverksgränssnitt från programsäkerhetsgrupper](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) eller [ta bort ett nätverksgränssnitt](virtual-network-network-interface.md#delete-a-network-interface).

1. Välj **alla tjänster** i övre vänstra hörnet i Azure Portal.
2. Ange *programsäkerhetsgrupper* i den **alla tjänster Filter** och väljer sedan **programsäkerhetsgrupper** när den visas i sökresultaten.
3. Välj den säkerhetsgrupp för programmet som du vill ta bort.
4. Välj **ta bort**, och välj sedan **Ja** att ta bort programsäkerhetsgruppen.

**Kommandon**

- Azure CLI: [az nätverket asg ta bort](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Behörigheter

För att utföra uppgifter på nätverkssäkerhetsgrupper, säkerhetsregler och programsäkerhetsgrupper måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som är tilldelade den behörighet som visas i följande tabell:

### <a name="network-security-group"></a>Nätverkssäkerhetsgrupp

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hämta nätverkssäkerhetsgrupp                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Skapa eller uppdatera nätverkssäkerhetsgrupp                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Ta bort nätverkssäkerhetsgruppen                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associera en nätverkssäkerhetsgrupp till ett undernät eller ett gränssnitt 


### <a name="network-security-group-rule"></a>Regel för nätverkssäkerhetsgrupp

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Hämta regel                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Skapa eller uppdatera regel                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Ta bort regel                                                         |

### <a name="application-security-group"></a>Programsäkerhetsgrupp

| Åtgärd                                                                     | Namn                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Ansluta en IP-konfiguration till en programsäkerhetsgrupp|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Ansluta en säkerhetsregel till en programsäkerhetsgrupp    |
| Microsoft.Network/applicationSecurityGroups/read                           | Hämta en programsäkerhetsgrupp                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Skapa eller uppdatera en programsäkerhetsgrupp           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Ta bort en programsäkerhetsgrupp                     |

## <a name="next-steps"></a>Nästa steg

- Skapa ett nätverk eller programmet hjälp av [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk
