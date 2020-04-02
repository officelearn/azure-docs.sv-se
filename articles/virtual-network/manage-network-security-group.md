---
title: Skapa, ändra eller ta bort en säkerhetsgrupp för Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar, ändrar eller tar bort en nätverkssäkerhetsgrupp.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521035"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp

Med säkerhetsregler i nätverkssäkerhetsgrupper kan du filtrera den typ av nätverkstrafik som kan flöda in och ut från virtuella nätverksundernät och nätverksgränssnitt. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md). Slutför sedan [självstudiekursen Filter-nätverkstrafik](tutorial-filter-network-traffic.md) för att få viss erfarenhet av nätverkssäkerhetsgrupper.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har ett, konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför en av dessa uppgifter innan du påbörjar resten av den här artikeln:

- **Portalanvändare**: Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare:** Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Leta reda på listrutan **Välj miljö** i webbläsaren Azure Cloud Shell och välj sedan **PowerShell** om det inte redan är markerat.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

- **CLI-användare (Azure Command-line Interface)**: Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.28 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` för att skapa en anslutning med Azure.

Kontot som du loggar in på eller ansluter till Azure med måste tilldelas [rollen Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="work-with-network-security-groups"></a>Arbeta med nätverkssäkerhetsgrupper

Du kan skapa, [visa alla](#view-all-network-security-groups), visa [information om](#view-details-of-a-network-security-group), [ändra](#change-a-network-security-group)och [ta bort](#delete-a-network-security-group) en nätverkssäkerhetsgrupp. Du kan också associera eller koppla bort en [nätverkssäkerhetsgrupp](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) från ett nätverksgränssnitt eller undernät.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Det finns en gräns för hur många nätverkssäkerhetsgrupper du kan skapa för varje Azure-plats och prenumeration. Mer information finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. På [Azure-portalmenyn](https://portal.azure.com) eller på **startsidan** väljer du **Skapa en resurs**.

2. Välj **Nätverk**och välj sedan **Nätverkssäkerhetsgrupp**.

3. Ange värden för följande inställningar under fliken **Grunderna** på sidan **Skapa nätverkssäkerhetsgrupp:**

    | Inställning | Åtgärd |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj en befintlig resursgrupp eller välj **Skapa ny** om du vill skapa en ny resursgrupp. |
    | **Namn** | Ange en unik textsträng i en resursgrupp. |
    | **Regionen** | Välj den plats du vill använda. |

4. Välj **Granska + skapa**.

5. När du har skickat meddelandet **Validering** väljer du **Skapa**.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Visa alla nätverkssäkerhetsgrupper

Gå till [Azure-portalen](https://portal.azure.com) för att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper**. Listan över nätverkssäkerhetsgrupper visas för din prenumeration.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nsg lista](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Visa information om en nätverkssäkerhetsgrupp

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper**.

2. Välj namnet på nätverkssäkerhetsgruppen.

På menyraden i nätverkssäkerhetsgruppen under **Inställningar**kan du visa de **inkommande säkerhetsreglerna,** **utgående säkerhetsregler,** **nätverksgränssnitt**och undernät som nätverkssäkerhetsgruppen är associerad med. **Subnets**

Under **Övervakning**kan du aktivera eller inaktivera **diagnostikinställningar**. Under **Support + felsökning**kan du visa effektiva **säkerhetsregler**. Mer information finns i [Diagnostikloggning för en nätverkssäkerhetsgrupp](virtual-network-nsg-manage-log.md) och [diagnostisera ett problem med fel på vm-nätverkstrafikfilter](diagnose-network-traffic-filter-problem.md).

Mer information om de vanliga Azure-inställningarna i listan finns i följande artiklar:

- [Aktivitetslogg](../azure-monitor/platform/platform-logs-overview.md)
- [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
- [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automationsskript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nsg visa](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Ändra en nätverkssäkerhetsgrupp

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper**.

2. Markera namnet på den nätverkssäkerhetsgrupp som du vill ändra.

De vanligaste ändringarna är att lägga till [en säkerhetsregel,](#create-a-security-rule) [ta bort en regel](#delete-a-security-rule)och associera eller koppla bort en [nätverkssäkerhetsgrupp till eller från ett undernät eller nätverksgränssnitt](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nsg uppdatering](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associera eller koppla bort en nätverkssäkerhetsgrupp till eller från ett undernät eller nätverksgränssnitt

Om du vill koppla en nätverkssäkerhetsgrupp till eller ta bort en nätverkssäkerhetsgrupp från ett nätverksgränssnitt läser [du Associera en nätverkssäkerhetsgrupp till eller koppla från en nätverkssäkerhetsgrupp från ett nätverksgränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Om du vill koppla en nätverkssäkerhetsgrupp till eller ta bort en nätverkssäkerhetsgrupp från ett undernät finns i [Ändra undernätsinställningar](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ta bort en nätverkssäkerhetsgrupp

Om en nätverkssäkerhetsgrupp är kopplad till undernät eller nätverksgränssnitt kan den inte tas bort. Ta bort en nätverkssäkerhetsgrupp från alla undernät och nätverksgränssnitt innan du försöker ta bort den.

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper**.

2. Markera namnet på den nätverkssäkerhetsgrupp som du vill ta bort.

3. Välj **Ta bort**i verktygsfältet Nätverkssäkerhetsgruppen. Välj sedan **Ja** i bekräftelsedialogrutan.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nsg ta bort](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Ta bort-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Arbeta med säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller fler säkerhetsregler. Du kan skapa, [visa alla](#view-all-security-rules), visa [information om](#view-details-of-a-security-rule), [ändra](#change-a-security-rule)och [ta bort](#delete-a-security-rule) en säkerhetsregel.

### <a name="create-a-security-rule"></a>Skapa en säkerhetsregel

Det finns en gräns för hur många regler per nätverkssäkerhetsgrupp som du kan skapa för varje Azure-plats och prenumeration. Mer information finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa dina nätverkssäkerhetsgrupper. Sök efter och välj **Nätverkssäkerhetsgrupper**.

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill lägga till en säkerhetsregel i.

3. Välj **Inkommande säkerhetsregler** eller **Utgående säkerhetsregler**i nätverkssäkerhetsgruppens menyrad.

    Flera befintliga regler visas, inklusive några som du kanske inte har lagt till. När du skapar en nätverkssäkerhetsgrupp skapas flera standardsäkerhetsregler i den. Mer information finns i [standardsäkerhetsregler](security-overview.md#default-security-rules).  Du kan inte ta bort standardsäkerhetsregler, men du kan åsidosätta dem med regler som har högre prioritet.

4. <a name="security-rule-settings"></a>Välj **Lägg till**. Markera eller lägga till värden för följande inställningar och välj sedan **OK:**

    | Inställning | Värde | Information |
    | ------- | ----- | ------- |
    | **Källkod** | En av:<ul><li>**Alla**</li><li>**IP-adresser**</li><li>**Service Tag** (ingående säkerhetsregel) eller **VirtualNetwork** (utgående säkerhetsregel)</li><li>**&nbsp;Säkerhetsgrupp&nbsp;för program**</li></ul> | <p>Om du väljer **IP-adresser**måste du också ange **käll-IP-adresser/CIDR-intervall**.</p><p>Om du väljer **Service Tag**kan du också välja ett **källtjänstmärke**.</p><p>Om du väljer **Programsäkerhetsgrupp**måste du också välja en befintlig programsäkerhetsgrupp. Om du väljer **Programsäkerhetsgrupp** för både **Källa** och **Mål**måste nätverksgränssnitten inom båda programsäkerhetsgrupperna finnas i samma virtuella nätverk.</p> |
    | **Käll-IP-adresser/CIDR-intervall** | En kommaavgränsad lista över IP-adresser och klasslösa cidr-intervall (Interdomain Routing) | <p>Den här inställningen visas om du ändrar **Källa** till **IP-adresser**. Du måste ange ett enda värde eller en kommaavgränsad lista med flera värden. Ett exempel på `10.0.0.0/16, 192.188.1.1`flera värden är . Det finns gränser för hur många värden du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Om IP-adressen du anger tilldelas en Virtuell Azure-dator anger du dess privata IP-adress, inte dess offentliga IP-adress. Azure bearbetar säkerhetsregler efter att den har översatt den offentliga IP-adressen till en privat IP-adress för inkommande säkerhetsregler, men innan den översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-adresstyper](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Servicetag för källa** | Ett servicetag från listrutan | Den här valfria inställningen visas om du anger **Source** to **Service Tag** för en inkommande säkerhetsregel. En tjänsttagg är en fördefinierad identifierare för en kategori av IP-adresser. Mer information om tillgängliga tjänsttaggar och vad varje tagg representerar finns i [Tjänsttaggar](security-overview.md#service-tags). |
    | **Säkerhetsgrupp för källprogram** | En befintlig programsäkerhetsgrupp | Den här inställningen visas om du anger **säkerhetsgrupp För Källa** **till Program**. Välj en programsäkerhetsgrupp som finns i samma region som nätverksgränssnittet. Lär dig hur du [skapar en programsäkerhetsgrupp](#create-an-application-security-group). |
    | **Källportintervall** | En av:<ul><li>En enda port, t.ex.`80`</li><li>En rad hamnar, t.ex.`1024-65535`</li><li>En kommaavgränsad lista över enstaka portar och/eller portintervall, t.ex.`80, 1024-65535`</li><li>En asterisk`*`( ) för att tillåta trafik på alla hamnar</li></ul> | Den här inställningen anger de portar där regeln tillåter eller nekar trafik. Det finns gränser för hur många portar du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Destination** | En av:<ul><li>**Alla**</li><li>**IP-adresser**</li><li>**Service Tag** (utgående säkerhetsregel) eller **VirtualNetwork** (ingående säkerhetsregel)</li><li>**&nbsp;Säkerhetsgrupp&nbsp;för program**</li></ul> | <p>Om du väljer **IP-adresser**anger du även **mål-IP-adresser/CIDR-intervall**.</p><p>Om du väljer **VirtualNetwork**tillåts trafik till alla IP-adresser i det virtuella nätverkets adressutrymme. **VirtualNetwork** är ett servicetag.</p><p>Om du väljer **Programsäkerhetsgrupp**måste du sedan välja en befintlig programsäkerhetsgrupp. Lär dig hur du [skapar en programsäkerhetsgrupp](#create-an-application-security-group).</p> |
    | **Mål-IP-adresser/CIDR-intervall** | En kommaavgränsad lista över IP-adresser och CIDR-intervall | <p>Den här inställningen visas om du ändrar **mål** till **IP-adresser**. I likhet med **ursprungs-** och **käll-IP-adresser/CIDR-intervall**kan du ange en eller flera adresser eller intervall. Det finns gränser för det nummer du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Om IP-adressen du anger tilldelas en Virtuell Azure-dator, se till att du anger dess privata IP, inte dess offentliga IP-adress. Azure bearbetar säkerhetsregler efter att den har översatt den offentliga IP-adressen till en privat IP-adress för inkommande säkerhetsregler, men innan Azure översätter en privat IP-adress till en offentlig IP-adress för utgående regler. Mer information om offentliga och privata IP-adresser i Azure finns i [IP-adresstyper](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Måltjänsttagg** | Ett servicetag från listrutan | Den här valfria inställningen visas om du ändrar **mål** till **Service Tag** för en utgående säkerhetsregel. En tjänsttagg är en fördefinierad identifierare för en kategori av IP-adresser. Mer information om tillgängliga tjänsttaggar och vad varje tagg representerar finns i [Tjänsttaggar](security-overview.md#service-tags). |
    | **Säkerhetsgrupp för målprogram** | En befintlig programsäkerhetsgrupp | Den här inställningen visas om du anger **mål** **till programsäkerhetsgrupp**. Välj en programsäkerhetsgrupp som finns i samma region som nätverksgränssnittet. Lär dig hur du [skapar en programsäkerhetsgrupp](#create-an-application-security-group). |
    | **Målportintervall** | En av:<ul><li>En enda port, t.ex.`80`</li><li>En rad hamnar, t.ex.`1024-65535`</li><li>En kommaavgränsad lista över enstaka portar och/eller portintervall, t.ex.`80, 1024-65535`</li><li>En asterisk`*`( ) för att tillåta trafik på alla hamnar</li></ul> | Precis som med **källportintervall**kan du ange en eller flera portar och intervall. Det finns gränser för det nummer du kan ange. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokollet** | **Alla**, **TCP,** **UDP**eller **ICMP** | Du kan begränsa regeln till TCP (Transmission Control Protocol), UP (User Datagram Protocol) eller Internet Control Message Protocol (ICMP). Standardinställningen är att regeln ska gälla för alla protokoll. |
    | **Åtgärd** | **Tillåt** eller **neka** | Den här inställningen anger om den här regeln tillåter eller nekar åtkomst för den angivna käll- och målkonfigurationen. |
    | **Prioritet** | Ett värde mellan 100 och 4096 som är unikt för alla säkerhetsregler inom nätverkssäkerhetsgruppen | Azure bearbetar säkerhetsregler i prioritetsordning. Ju lägre siffra, desto högre prioritet. Vi rekommenderar att du lämnar ett mellanrum mellan prioritetsnummer när du skapar regler, till exempel 100, 200 och 300. Om du lämnar luckor blir det enklare att lägga till regler i framtiden, så att du kan ge dem högre eller lägre prioritet än befintliga regler. |
    | **Namn** | Ett unikt namn för regeln inom nätverkssäkerhetsgruppen | Namnet kan vara upp till 80 tecken. Det måste börja med en bokstav eller ett nummer, och det måste sluta med en bokstav, ett nummer eller ett understreck. Namnet får bara innehålla bokstäver, siffror, understreck, punkter eller bindestreck. |
    | **Beskrivning** | En textbeskrivning | Du kan också ange en textbeskrivning för säkerhetsregeln. |

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Visa alla säkerhetsregler

En nätverkssäkerhetsgrupp innehåller noll eller fler regler. Mer information om informationen i listan när du visar regler finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md).

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa reglerna för en nätverkssäkerhetsgrupp. Sök efter och välj **Nätverkssäkerhetsgrupper**.

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill visa reglerna för.

3. Välj **Inkommande säkerhetsregler** eller **Utgående säkerhetsregler**i nätverkssäkerhetsgruppens menyrad.

Listan innehåller alla regler som du har skapat och nätverkssäkerhetsgruppens [standardsäkerhetsregler](security-overview.md#default-security-rules).

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa reglerna för en nätverkssäkerhetsgrupp. Sök efter och välj **Nätverkssäkerhetsgrupper**.

2. Välj namnet på den nätverkssäkerhetsgrupp som du vill visa information om en regel för.

3. Välj **Inkommande säkerhetsregler** eller **Utgående säkerhetsregler**i nätverkssäkerhetsgruppens menyrad.

4. Välj den regel som du vill visa information för. En förklaring av alla inställningar finns i [Säkerhetsregelinställningar](#security-rule-settings).

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhetsregel. Det fungerar inte om du väljer en standardsäkerhetsregel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nsg regel visar](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Ändra en säkerhetsregel

1. Slutför stegen i [Visa information om en säkerhetsregel](#view-details-of-a-security-rule).

2. Ändra inställningarna efter behov och välj sedan **Spara**. En förklaring av alla inställningar finns i [Säkerhetsregelinställningar](#security-rule-settings).

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhetsregel. Du får inte ändra en standardsäkerhetsregel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Ta bort en säkerhetsregel

1. Slutför stegen i [Visa information om en säkerhetsregel](#view-details-of-a-security-rule).

2. Välj **Ta bort** och välj sedan **Ja**.

    > [!NOTE]
    > Den här proceduren gäller endast för en anpassad säkerhetsregel. Du får inte ta bort en standardsäkerhetsregel.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk nsg regel ta bort](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Ta bort-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Arbeta med programsäkerhetsgrupper

En programsäkerhetsgrupp innehåller noll eller fler nätverksgränssnitt. Mer information finns i [programsäkerhetsgrupper](security-overview.md#application-security-groups). Alla nätverksgränssnitt i en programsäkerhetsgrupp måste finnas i samma virtuella nätverk. Mer information om hur du lägger till ett nätverksgränssnitt i en programsäkerhetsgrupp finns i [Lägga till ett nätverksgränssnitt i en programsäkerhetsgrupp](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Skapa en programsäkerhetsgrupp

1. På [Azure-portalmenyn](https://portal.azure.com) eller på **startsidan** väljer du **Skapa en resurs**.

2. Ange *säkerhetsgrupp för program i*sökrutan .

3. På sidan **Programsäkerhetsgrupp** väljer du **Skapa**.

4. Ange värden för följande inställningar under fliken **Grunderna** på sidan **Skapa ett programs säkerhetsgrupp:**

    | Inställning | Åtgärd |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj en befintlig resursgrupp eller välj **Skapa ny** om du vill skapa en ny resursgrupp. |
    | **Namn** | Ange en unik textsträng i en resursgrupp. |
    | **Regionen** | Välj den plats du vill använda. |

5. Välj **Granska + skapa**.

6. Välj **Skapa**när du har skickat meddelandet **Validering** under fliken **Granska + skapa** .

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk asg skapa](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [Nya-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Visa alla programsäkerhetsgrupper

Gå till [Azure-portalen](https://portal.azure.com) för att visa dina programsäkerhetsgrupper. Sök efter och välj **Programsäkerhetsgrupper**. Azure-portalen visar en lista över dina programsäkerhetsgrupper.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk asg lista](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Visa information om en viss programsäkerhetsgrupp

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa en programsäkerhetsgrupp. Sök efter och välj **Programsäkerhetsgrupper**.

2. Välj namnet på den programsäkerhetsgrupp som du vill visa information om.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Ändra en programsäkerhetsgrupp

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa en programsäkerhetsgrupp. Sök efter och välj **Programsäkerhetsgrupper**.

2. Markera namnet på den programsäkerhetsgrupp som du vill ändra.

3. Markera **ändra** bredvid den inställning som du vill ändra. Du kan till exempel lägga till eller ta bort **taggar**eller ändra **resursgruppen** eller **prenumerationen**.

    > [!NOTE]
    > Du kan inte ändra platsen.

    I menyraden kan du också välja **Access control (IAM)**. På sidan **Åtkomstkontroll (IAM)** kan du tilldela eller ta bort behörigheter till programsäkerhetsgruppen.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk asg uppdatering](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Ingen PowerShell-cmdlet |

### <a name="delete-an-application-security-group"></a>Ta bort en programsäkerhetsgrupp

Du kan inte ta bort en programsäkerhetsgrupp om den innehåller några nätverksgränssnitt. Om du vill ta bort alla nätverksgränssnitt från programsäkerhetsgruppen ändrar du antingen nätverksgränssnittsinställningarna eller tar bort nätverksgränssnitten. Mer information finns i [Lägga till eller ta bort från programsäkerhetsgrupper](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) eller Ta bort ett [nätverksgränssnitt](virtual-network-network-interface.md#delete-a-network-interface).

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera dina programsäkerhetsgrupper. Sök efter och välj **Programsäkerhetsgrupper**.

2. Markera namnet på den programsäkerhetsgrupp som du vill ta bort.

3. Välj **Ta bort**och välj sedan **Ja** om du vill ta bort programsäkerhetsgruppen.

#### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az nätverk asg ta bort](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Ta bort-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i nätverkssäkerhetsgrupper, säkerhetsregler och programsäkerhetsgrupper måste ditt konto tilldelas rollen [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats rätt behörigheter enligt följande tabeller:

### <a name="network-security-group"></a>Nätverkssäkerhetsgrupp

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hämta nätverkssäkerhetsgrupp                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Skapa eller uppdatera nätverkssäkerhetsgrupp                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Ta bort nätverkssäkerhetsgrupp                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associera en nätverkssäkerhetsgrupp till ett undernät eller nätverksgränssnitt 

### <a name="network-security-group-rule"></a>Regel för nätverkssäkerhetsgrupp

| Åtgärd                                                        |   Namn                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read Microsoft.Network/networkSecurityGroups/rules/read Microsoft.Network/networkSecurityGroups/rules/read Microsoft.            |   Hämta regel                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Skapa eller uppdatera regel                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Ta bort regel                                                         |

### <a name="application-security-group"></a>Programsäkerhetsgrupp

| Åtgärd                                                                     | Namn                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Ansluta en IP-konfiguration till en programsäkerhetsgrupp|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/åtgärd | Ansluta en säkerhetsregel till en programsäkerhetsgrupp    |
| Microsoft.Network/applicationSecurityGroups/read                           | Skaffa en programsäkerhetsgrupp                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Skapa eller uppdatera en programsäkerhetsgrupp           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Ta bort en programsäkerhetsgrupp                     |

## <a name="next-steps"></a>Nästa steg

- Skapa en nätverks- eller programsäkerhetsgrupp med [powershell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk
