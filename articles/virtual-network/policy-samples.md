---
title: Exempel på principmall | Microsoft Docs
description: Exempel på Azure-principmall för virtuella nätverk.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8228da778e6cf6a874868ef0206092913908352d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694294"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Exempel på Azure-principmallar för virtuellt nätverk

Följande tabell innehåller länkar till exempel för [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Exemplen finns i [lagringsplatsen för Azure Policy-exempel](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Nätverk**||
| [NSG X på varje NIC](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att en viss nätverkssäkerhetsgrupp används med varje gränssnitt för virtuellt nätverk. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [NSG X i varje undernät](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att en viss nätverkssäkerhetsgrupp används med varje virtuella undernät. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Ingen routningstabell](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Förbjuder att virtuella nätverk distribueras med en routningstabell. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |
|**Övervakning**||
| [Granska diagnostikinställning](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Granskar om diagnostikinställningarna inte är aktiverade för angivna resurstyper. Du anger en matris med resurstyper för att kontrollera om diagnostikinställningarna är aktiverade. |
|**Konventioner för namn och text**||
| [Tillåt flera namnmönster](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Tillåt ett av många namnmönster som ska användas för resurser. |
| [Kräv like-mönster](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Se till att resursnamnen uppfyller *like*-villkoret för ett mönster. |
| [Kräv matchningsmönster](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Se till att resursnamnen matchar angivna namngivningsmönster. |
| [Kräv taggmatchningsmönster](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Se till att ett taggvärde matchar ett textmönster. |
|**Taggar**||
| [Principinitiativ för faktureringstaggar](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver angivna taggvärden för kostnadsställe och produktnamn. Använder inbyggda principer för att tillämpa och framtvinga taggar som krävs. Du anger de värden som krävs för taggar.  |
| [Lägg till tagg och dess värde i resursgrupper](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver en tagg och ett värde i en resursgrupp. Du anger taggnamn och värde som ska krävas.  |
| [Framtvinga tagg och dess värde](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver ett angivet taggnamn och värde. Du anger taggnamn och värde som ska framtvingas.  |
| [Använd taggen och dess standardvärde](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Lägger till ett angivet taggnamn och värde, om den taggen inte tillhandahålls. Du anger taggnamn och värde som ska gälla.  |
|**Allmänt**||
| [Tillåtna platser](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att alla resurser distribueras till de godkända platserna. Du anger en matris med godkända platser.  |
| [Tillåtna resurstyper](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garanterar att bara godkända resurstyper distribueras. Du anger en matris med resurstyper som tillåts.  |
| [Otillåtna resurstyper](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Förhindrar distributionen av angivna resurstyper. Du anger en matris med resurstyper som ska blockeras.  |