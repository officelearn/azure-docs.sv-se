---
title: IP-grupper i Azure-brandväggen
description: Med IP-grupper kan du gruppera och hantera IP-adresser för Azure Firewall-regler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: e0638cbccd5e3bc282dbdd7d3b5918e29081a12b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757159"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>IP-grupper (förhandsversion) i Azure-brandväggen

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med IP-grupper kan du gruppera och hantera IP-adresser för Azure-brandväggsregler på följande sätt:

- Som källadress i DNAT-regler
- Som källa eller måladress i nätverksregler
- Som källadress i programregler


En IP-grupp kan ha en enda IP-adress, flera IP-adresser eller ett eller flera IP-adressintervall.

IP-grupper kan återanvändas i Azure Firewall DNAT, nätverk och programregler för flera brandväggar i regioner och prenumerationer i Azure. Gruppnamnen måste vara unika. Du kan konfigurera en IP-grupp i Azure-portalen, Azure CLI eller REST API. En exempelmall tillhandahålls som hjälper dig att komma igång.

## <a name="sample-format"></a>Exempelformat

Följande IPv4-adressformatexempel är giltiga att använda i IP-grupper:

- Enkel adress: 10.0.0.0
- CIDR-notation: 10.1.0.0/32
- Adressintervall: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Skapa en IP-grupp

En IP-grupp kan skapas med Hjälp av Azure-portalen, Azure CLI eller REST API. Mer information finns i [Skapa en IP-grupp (förhandsgranskning)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Bläddra i IP-grupper
1. Skriv **IP-grupper** i sökfältet i Azure portal och välj dem. Du kan se listan över IP-grupper eller välja **Lägg till** för att skapa en ny IP-grupp.
2. Välj en IP-grupp för att öppna översiktssidan. Du kan redigera, lägga till eller ta bort IP-adresser eller IP-grupper.

   ![Översikt över IP-grupper](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Hantera en IP-grupp

Du kan se alla IP-adresser i IP-gruppen och de regler eller resurser som är associerade med den. Om du vill ta bort en IP-grupp måste du först ta bort IP-gruppen från resursen som använder den.

1. Om du vill visa eller redigera IP-adresserna väljer du **IP-adresser** under **Inställningar** i den vänstra rutan.
2. Om du vill lägga till en eller flera IP-adresser väljer du **Lägg till IP-adresser**. Då öppnas sidan **Dra eller bläddra** för en uppladdning, eller så kan du ange adressen manuellt.
3.    Välja ellipser (**...**) till höger för att redigera eller ta bort IP-adresser. Om du vill redigera eller ta bort flera IP-adresser markerar du rutorna och väljer **Redigera** eller **Ta bort** högst upp.
4. Slutligen kan exportera filen i CSV-filformatet.

> [!NOTE]
> Om du tar bort alla IP-adresser i en IP-grupp medan den fortfarande används i en regel hoppas den regeln över.


## <a name="use-an-ip-group"></a>Använda en IP-grupp

Du kan nu välja **IP-grupp** som **källtyp** eller **måltyp** för IP-adress(er) när du skapar Azure Firewall DNAT, program eller nätverksregler.

> [!NOTE]
> IP-grupper stöds inte i brandväggsprincipen. Det stöds för närvarande endast med traditionella brandväggsregler.

![IP-grupper i brandväggen](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Regional tillgänglighet

IP-grupper är tillgängliga i alla offentliga molnregioner.

## <a name="ip-address-limits"></a>BEGRÄNSNINGAR FÖR IP-adress

För 50 IP-grupper eller mindre kan du ha högst 5000 enskilda IP-adresser per brandväggsinstans. För 51 till 100 IP-grupper kan du ha 500 enskilda IP-adresser per brandväggsinstans.

### <a name="examples"></a>Exempel

#### <a name="example-1-supported"></a>Exempel 1: stöds

|IP-grupper  |# IP-adresser  |Notation  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Regel1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Regel1|
|IPGroup3     |1|1.2.3.4|Regel1|
|     |**Totalt 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>Exempel 2: stöds

|IP-grupper  |# IP-adresser  |Notation  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Regel1|
|IPGroup2     |4096|11.0.0.0/20|Regel1|
|     |**Totalt 8192**|         |         |

#### <a name="example-3-not-supported"></a>Exempel 3: stöds inte

|IP-grupper  |# IP-adresser  |Notation  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Regel1|
|     |**Totalt 8192**|||

#### <a name="example-4-supported"></a>Exempel 4: stöds

|IP-grupper  |# IP-adresser  |Notation  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Regel1|
|IPGroup2     |4096|11.0.0.0/20|Regel2|
|     |**Totalt 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>Relaterade Azure PowerShell-cmdlets

Följande Azure PowerShell-cmdlets kan användas för att skapa och hantera IP-grupper:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Ta bort-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Ny-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Nya-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Nya-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).