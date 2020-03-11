---
title: Uppdatering av Azure Traffic Analytics-schema – mars 2020 | Microsoft Docs
description: Exempel frågor med nya fält i Trafikanalys-schemat.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969067"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Exempel frågor med nya fält i Trafikanalys schema (uppdatering augusti 2019 schema)

[Trafikanalyss logg schema](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) har uppdaterats med följande nya fält: **SrcPublicIPs_s** , **DestPublicIPs_s**, **NSGRule_s**. Under de kommande månaderna kommer följande äldre fält att bli föråldrade: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, PublicIPs_s **, FlowCount_d**.
De nya fälten innehåller information om käll-och mål-IP-adresser och fören klar frågor.

Nedan visas tre exempel som visar hur du ersätter de gamla fälten med nya.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Exempel 1 – VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s, PublicIPs_s

Vi behöver inte ange käll-och mål ärenden för Azure och externa offentliga flöden från FlowDirection_s fält för AzurePublic-och ExternalPublic-flöden specifikt. I händelse av en NVA (virtuell nätverks installation) kan fältet FlowDirection_s också vara olämpligt att användas.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>Exempel 2 – NSGRules_s

Det tidigare fältet har formatet: < index Value 0) > | < NSG_RULENAME > |<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Tidigare använde vi för att samla data över NSG och NSGRules. Nu ska vi inte aggregera. Det betyder att NSGList_s bara innehåller en NSG och NSGRules_s även för att bara innehålla en regel. Vi har tagit bort den komplicerade formateringen här och samma finns i andra fält som anges nedan:

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>Exempel 3 – FlowCount_d

Eftersom vi inte är klubbiga data över NSG är FlowCount_d bara AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d.
Endast 1 av de 4 ovanstående är inte noll och resten av tre blir 0. Den anger status och antal i NÄTVERKSKORTet där flödet fångades.

Om flödet tilläts fylls ett av de fält som är prefixet "Allowed". Annars kommer ett fält som föregås av "nekad" att fyllas i.
Om flödet var inkommande, fylls ett av fälten med "\_d" som "InFlows_d" suffixet i fältet. Annars kommer "OutFlows_d" att fyllas i.

Beroende på över 2 villkor vet vi vilken som är en av 4 som kommer att fyllas i.


## <a name="next-steps"></a>Nästa steg
För att få svar på vanliga frågor, se [vanliga frågor och svar om trafik analys](traffic-analytics-faq.md) för att se information om funktioner, se [Traffic Analytics-dokumentation](traffic-analytics.md)
