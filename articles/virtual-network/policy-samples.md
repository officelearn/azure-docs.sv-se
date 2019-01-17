---
title: Exempel på principmall | Microsoft Docs
description: Exempel på Azure-principmall för virtuella nätverk.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: a0153cf11c3fe817ce397b4d0c47a786a4334f39
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214818"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Exempel på Azure-principmallar för virtuellt nätverk

Följande tabell innehåller länkar till [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-exempelmallar. Exemplen finns i [lagringsplatsen för Azure Policy-exempel](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Nätverk**||
| [NSG X på varje NIC](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att en viss nätverkssäkerhetsgrupp används med varje gränssnitt för virtuellt nätverk. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [NSG X i varje undernät](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att en viss nätverkssäkerhetsgrupp används med varje virtuella undernät. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Ingen routningstabell](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Förbjuder att virtuella nätverk distribueras med en routningstabell. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |
|**Övervakning**||
| [Granska diagnostikinställning](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Granskar om diagnostikinställningarna inte är aktiverade för angivna resurstyper. Du anger en matris med resurstyper för att kontrollera om diagnostikinställningarna är aktiverade. |
|**Konventioner för namn och text**||
| [Tillåt flera namnmönster](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Tillåt ett av många namnmönster som ska användas för resurser. |
| [Kräv like-mönster](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Se till att resursnamnen uppfyller *like*-villkoret för ett mönster. |
| [Kräv matchningsmönster](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Se till att resursnamnen matchar angivna namngivningsmönster. |
| [Kräv taggmatchningsmönster](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Se till att ett taggvärde matchar ett textmönster. |
|**Taggar**||
| [Principinitiativ för faktureringstaggar](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver angivna taggvärden för kostnadsställe och produktnamn. Använder inbyggda principer för att tillämpa och framtvinga taggar som krävs. Du anger de värden som krävs för taggar.  |
| [Lägg till tagg och dess värde i resursgrupper](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver en tagg och ett värde i en resursgrupp. Du anger taggnamn och värde som ska krävas.  |
| [Framtvinga tagg och dess värde](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver ett angivet taggnamn och värde. Du anger taggnamn och värde som ska framtvingas.  |
| [Använd taggen och dess standardvärde](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Lägger till ett angivet taggnamn och värde, om den taggen inte tillhandahålls. Du anger taggnamn och värde som ska gälla.  |
|**Allmänt**||
| [Tillåtna platser](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kräver att alla resurser distribueras till de godkända platserna. Du anger en matris med godkända platser.  |
| [Tillåtna resurstyper](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garanterar att bara godkända resurstyper distribueras. Du anger en matris med resurstyper som tillåts.  |
| [Otillåtna resurstyper](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Förhindrar distributionen av angivna resurstyper. Du anger en matris med resurstyper som ska blockeras.  |