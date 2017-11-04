---
title: "Övervaka åtgärder, händelser och prestandaräknare för NSG: er | Microsoft Docs"
description: "Lär dig hur du aktiverar räknare, händelser och operativa loggning för NSG: er"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Log Analytics för nätverkssäkerhetsgrupper (NSG:er)

Du kan aktivera följande diagnostiska loggen kategorier för NSG: er:

* **Händelse:** innehåller poster för vilka NSG-regler tillämpas på virtuella datorer och instansen roller baserat på MAC-adress. Status för de här reglerna samlas var 60: e sekund.
* **Regelräknare:** innehåller poster för hur många gånger varje NSG-regel används för att neka eller Tillåt trafiken.

> [!NOTE]
> Diagnostikloggar är bara tillgängliga för NSG: er som distribueras via Azure Resource Manager-distributionsmodellen. Du kan inte aktivera diagnostikloggning för NSG: er som distribueras via den klassiska distributionsmodellen. För bättre förståelse av de två modellerna, referera till den [förstå Azure distributionsmodeller](../resource-manager-deployment-model.md) artikel.

Aktivitetsloggning (tidigare kallat granskningsläge eller operativa loggar) är aktiverad som standard för NSG: er som skapats via antingen Azure distributionsmodell. För att avgöra vilka åtgärder har slutförts på NSG: er i aktivitetsloggen, leta efter poster som innehåller följande resurstyper: 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Läs den [översikt över Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) artikeln om du vill veta mer om aktivitetsloggar. 

## <a name="enable-diagnostic-logging"></a>Aktivera diagnostikloggning

Diagnostikloggning måste vara aktiverat för *varje* NSG som du vill samla in data för. Den [översikt av Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artikeln förklarar var diagnostikloggar skickas. Om du inte har en befintlig NSG slutföra stegen i den [skapar en nätverkssäkerhetsgrupp](virtual-networks-create-nsg-arm-pportal.md) artikeln om du vill skapa en. Du kan aktivera NSG loggning med hjälp av följande metoder:

### <a name="azure-portal"></a>Azure Portal

Använda portalen för att aktivera loggning, logga in på den [portal](https://portal.azure.com). Klicka på **fler tjänster**, Skriv *nätverkssäkerhetsgrupper*. Välj NSG: N som du vill aktivera loggning för. Följ instruktionerna för icke-beräkningsresurser i den [aktivera diagnostikloggar i portalen](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) artikel. Välj **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter**, eller båda kategorier av loggar.

### <a name="powershell"></a>PowerShell

Om du vill använda PowerShell för att aktivera loggning, följ instruktionerna i den [aktivera diagnostikloggar via PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) artikel. Utvärdera följande information innan du anger ett kommando från artikeln:

- Du kan fastställa det värde som ska användas för den `-ResourceId` parameter genom att ersätta följande [text] på lämpligt sätt att ange kommandot `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. ID-utdata från kommandot liknar */subscriptions/ [prenumeration Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]*.
- Om du vill samla in data från loggen kategori lägger du till `-Categories [category]` till slutet av kommandot i artikeln, där kategorin är antingen *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du inte använder den `-Categories` parameter, datainsamling har aktiverats för både loggfilen kategorier.

### <a name="azure-command-line-interface-cli"></a>Azure-kommandoradsgränssnittet (CLI)

Om du vill använda CLI för att aktivera loggning, följ instruktionerna i den [aktivera diagnostikloggar via CLI](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) artikel. Utvärdera följande information innan du anger ett kommando från artikeln:

- Du kan fastställa det värde som ska användas för den `-ResourceId` parameter genom att ersätta följande [text] på lämpligt sätt att ange kommandot `azure network nsg show [resource-group-name] [nsg-name]`. ID-utdata från kommandot liknar */subscriptions/ [prenumeration Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]*.
- Om du vill samla in data från loggen kategori lägger du till `-Categories [category]` till slutet av kommandot i artikeln, där kategorin är antingen *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du inte använder den `-Categories` parameter, datainsamling har aktiverats för både loggfilen kategorier.

## <a name="logged-data"></a>Loggdata

JSON-formaterade data skrivs för båda loggar. De specifika data som skrivs för varje loggtyp av visas i följande avsnitt:

### <a name="event-log"></a>Händelseloggen
Den här loggfilen innehåller information om vilka NSG-regler tillämpas på virtuella datorer och cloud service rollinstanser baserat på MAC-adress. Följande exempeldata loggas för varje händelse:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Logg för räknaren

Den här loggfilen innehåller information om varje regel tillämpas på resurser. Följande exempeldata loggas varje gång som ska tillämpas:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Visa och analysera loggar

Om du vill veta hur du visar loggdata för aktiviteten Läs den [översikt över Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artikel. Om du vill veta hur du visar diagnostiska loggdata läsa den [översikt av Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artikel. Om du skickar diagnostikdata till logganalys, kan du använda den [Azure Network Security Group analytics](../log-analytics/log-analytics-azure-networking-analytics.md) lösning för förbättrad insights (förhandsversion). 
