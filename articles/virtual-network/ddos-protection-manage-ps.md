---
title: "Hantera Azure DDoS-skydd Standard med hjälp av Azure PowerShell | Microsoft Docs"
description: "Lär dig hur du hanterar Azure DDoS-skydd Standard med hjälp av Azure PowerShell."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: a1a3688d4ff215d05d2f78cdfa7d402e3fc20be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Hantera Azure DDoS-skydd Standard med hjälp av Azure PowerShell

>[!IMPORTANT]
>Azure DDoS-skydd Standard (DDoS-skydd) är för närvarande under förhandsgranskning. Ett begränsat antal Azure-resurser stöd DDoS-skydd, och i ett begränsat antal regioner. Du behöver [registrera dig för tjänsten](http://aka.ms/ddosprotection) under begränsad förhandsgranskningen att hämta DDoS-skydd är aktiverat för din prenumeration. Du kontaktas av Azure DDoS-teamet vid registreringen och vägleder dig genom processen för aktivering. DDoS-skydd finns tillgänglig i oss Öst oss Väst och West centrala oss regioner. Under förhandsgranskningen gör debiteras inte du för att använda tjänsten.

Den här artikeln visar hur du använder Azure PowerShell för att aktivera DDoS-skydd, inaktivera DDoS-skydd och använda telemetri för att minimera angreppet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Om du behöver installera eller uppgradera Azure PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>Aktivera DDoS-skydd

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Skapa ett nytt virtuellt nätverk och aktivera DDoS-skydd

Om du vill skapa ett virtuellt nätverk med DDoS-skydd är aktiverat, kör du följande exempel:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

Det här exemplet skapar ett virtuellt nätverk med två undernät och två DNS-servrar. Effekten av att ange de DNS-servrarna i det virtuella nätverket är att de nätverkskort/virtuella datorer som har distribuerats i det här virtuella nätverket ärver dessa DNS-servrar som standard. DDoS-skydd är aktiverat för alla skyddade resurser i det virtuella nätverket.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Aktivera DDoS-skydd på ett befintligt virtuellt nätverk

Om du vill aktivera DDoS-skydd på ett befintligt virtuellt nätverk, kör du följande exempel:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Inaktivera DDoS-skydd på ett virtuellt nätverk

Om du vill inaktivera DDoS-skydd i ett virtuellt nätverk, kör du följande exempel:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>Granska statusen för DDoS-skydd för virtuella nätverk 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Använd telemetri för DDoS-skydd

Telemetri för en attack tillhandahålls via Azure-Monitor i realtid. Telemetrin är tillgänglig enbart för den varaktighet som en offentlig IP-adress är under lösning. Telemetri visas inte före eller efter en attack minskas.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Konfigurera aviseringar för mått för DDoS-skydd

Utnyttja Azure-Monitor varningskonfigurationen, kan du välja någon av de tillgängliga måtten DDoS-skydd att varna när det finns en aktiv migrering under ett angrepp.

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>Konfigurera e-Varningsregler via Azure PowerShell

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

3. Om du vill skapa en regel som du behöver ha flera viktiga uppgifter för först. 

    - Resurs-ID för resursen som du vill aktivera en avisering för.
    - Måttdefinitionerna som är tillgängliga för den här resursen. Ett sätt att hämta resurs-ID är att använda Azure-portalen. Under förutsättning att resursen har redan skapats, väljer du den i Azure-portalen. Klicka på nästa sida *egenskaper* under den *inställningar* avsnitt. Den **resurs-ID** är ett fält i nästa sida. Ett annat sätt är att använda den [resursutforskaren Azure](https://resources.azure.com/). Ett exempel resurs-ID för en offentlig IP-adress är:`/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    I följande exempel ställer in en avisering på en offentlig IP-adress som är kopplat till ett virtuellt nätverk. Att skapa en avisering på måttet är **Under DDoS-attacker eller inte**. Detta är ett booleskt värde 1 eller 0. En **1** innebär du angripet. En **0** innebär att du inte är angripet. Aviseringen skapas när du är angripna inom de senaste 5 minuterna.

    Om du vill skapa en webhook eller skicka e-postmeddelande när en avisering skapas måste du först skapa e-post och/eller webhooks. Sedan omedelbart skapa regel efteråt med taggen - åtgärder och som visas i följande exempel. Du kan inte associera webhook eller e-post med redan skapat regler med hjälp av PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. Kontrollera att dina aviseringar genom att titta på enskilda regler har skapats korrekt.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

Du kan också lära dig mer om [hur du konfigurerar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) och [logikappar](../logic-apps/logic-apps-what-are-logic-apps.md) för att skapa aviseringar.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Konfigurera loggning på mått för DDoS-skydd

Referera till den [PowerShell snabb start prover](../monitoring-and-diagnostics/insights-powershell-samples.md) som hjälper dig att komma åt och konfigurera Azure loggning via PowerShell.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)