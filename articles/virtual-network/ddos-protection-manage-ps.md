---
title: "Hantera Azure DDoS-skydd Standard med hjälp av Azure PowerShell | Microsoft Docs"
description: "Lär dig hur du hanterar Azure DDoS-skydd Standard med hjälp av Azure PowerShell."
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
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 33ff6cfcacd1632dc49b448e70361e1cb2ce1176
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Hantera Azure DDoS-skydd Standard med hjälp av Azure PowerShell

Lär dig mer om att aktivera och inaktivera för tjänsten (DDoS)-skydd och använda telemetri för att minska en DDoS-attack med Azure DDoS-skydd Standard. DDoS-skydd Standard skyddar Azure-resurser som virtuella datorer, belastningsutjämnare och programgatewayer som har en Azure [offentliga IP-adressen](virtual-network-public-ip-address.md) kopplade till den. Läs mer om DDoS-skydd Standard och dess funktioner i [DDoS-skydd Standard översikt](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS-skydd Standard (DDoS-skydd) är för närvarande under förhandsgranskning. Ett begränsat antal Azure-resurser stöd för DDoS-skydd och det är endast tillgänglig i ett begränsat antal regioner. En lista över tillgängliga regioner, se [DDoS-skydd Standard översikt](ddos-protection-overview.md). Du behöver [registrera dig för tjänsten](http://aka.ms/ddosprotection) under begränsad förhandsgranskningen att hämta DDoS-skydd är aktiverat för din prenumeration. Efter registrering kontaktas du av Azure DDoS-team som vägleder dig genom processen för aktivering.


## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Om du behöver installera eller uppgradera Azure PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Aktivera DDoS-skydd Standard - nytt virtuellt nätverk

Om du vill skapa ett virtuellt nätverk med DDoS-skydd är aktiverat, kör du följande exempel:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

Det här exemplet skapar ett virtuellt nätverk med två undernät och två DNS-servrar. Effekten av att ange de DNS-servrarna i det virtuella nätverket är att de nätverkskort/virtuella datorer som har distribuerats i det här virtuella nätverket ärver dessa DNS-servrar som standard. DDoS-skydd är aktiverat för alla skyddade resurser i det virtuella nätverket.

> [!WARNING]
> När du väljer en region väljer du en region som stöds i listan i [Azure DDoS-skydd Standard översikt](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Aktivera DDoS-skydd på ett befintligt virtuellt nätverk

Om du vill aktivera DDoS-skydd på ett befintligt virtuellt nätverk, kör du följande exempel:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> Det virtuella nätverket måste finnas i en region som stöds. En lista över regioner som stöds, se [Azure DDoS-skydd Standard översikt](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Inaktivera DDoS-skydd på ett virtuellt nätverk

Om du vill inaktivera DDoS-skydd i ett virtuellt nätverk, kör du följande exempel:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>Granska statusen för DDoS-skydd för ett virtuellt nätverk 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Använd telemetri för DDoS-skydd

Telemetri för en attack tillhandahålls via Azure-Monitor i realtid. Telemetrin är tillgänglig enbart för den varaktighet som en offentlig IP-adress är under lösning. Telemetri visas inte före eller efter en attack minskas.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Konfigurera aviseringar för mått för DDoS-skydd

Utnyttja Azure-Monitor varningskonfigurationen, kan du välja någon av de tillgängliga måtten DDoS-skydd att varna när det finns en aktiv migrering under ett angrepp.

#### <a name="configure-email-alert-rules-via-azure"></a>Konfigurera e-Varningsregler via Azure

1. Hämta en lista över prenumerationerna som finns tillgängliga. Kontrollera att du arbetar med rätt prenumerationen. Om du inte har det högra använda utdata från Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Om du vill visa en lista med befintliga regler på en resursgrupp, använder du följande kommando: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Om du vill skapa en regel måste du först ha följande information: 

    - Resurs-ID för resursen som du vill aktivera en avisering för.
    - Måttdefinitionerna som är tillgängliga för den här resursen. Ett sätt att hämta resurs-ID är att använda Azure-portalen. Under förutsättning att resursen har redan skapats, väljer du den i Azure-portalen. Klicka på nästa sida *egenskaper* under den *inställningar* avsnitt. Den **resurs-ID** är ett fält i nästa sida. Ett annat sätt är att använda den [resursutforskaren Azure](https://resources.azure.com/). Ett exempel resurs-ID för en offentlig IP-adress är:`/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    I följande exempel skapas en avisering för en offentlig IP-adress som är kopplad till en resurs i ett virtuellt nätverk. Att skapa en avisering på måttet är **Under DDoS-attacker eller inte**. Detta är ett booleskt värde 1 eller 0. En **1** innebär du angripet. En **0** innebär att du inte är angripet. Aviseringen skapas när en attack påbörjas inom de senaste 5 minuterna.

    Om du vill skapa en webhook eller skicka e-postmeddelande när en avisering skapas måste du först skapa e-post och/eller webhook. När du har skapat e-post eller webhook direkt skapa regeln med den `-Actions` tagg, som visas i följande exempel. Du kan inte associera webhook eller e-postmeddelanden med befintliga regler med hjälp av PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Kontrollera att aviseringen skapats korrekt genom att titta på regeln:

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

Du kan också lära dig mer om [hur du konfigurerar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [logikappar](../logic-apps/logic-apps-overview.md) för att skapa aviseringar.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Konfigurera loggning på mått för DDoS-skydd

Referera till den [PowerShell snabb start prover](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som hjälper dig att komma åt och konfigurera Azure loggning via PowerShell.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)