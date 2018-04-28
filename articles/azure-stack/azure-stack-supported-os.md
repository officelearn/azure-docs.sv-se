---
title: Operativsystem som stöds för Azure-Stack | Microsoft Docs
description: Dessa gästoperativsystem kan användas på Azure-stacken.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: ff3aea4e449e3d489b0c0f01345ecd9773c7d885
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Gästoperativsystem som stöds på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="windows"></a>Windows
Azure stacken stöder Windows-gästoperativsystem som listas i följande tabell: bilder i Marketplace finns tillgängliga för nedladdning till Azure-stacken. Avbildningar för Windows-klienten är inte tillgängliga på marknaden.

Under distributionen, lägger Azure Stack in en lämplig version av gästagenten i bilden.

| Operativsystem | Beskrivning | Tillgänglig i Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, version 1709 | 64-bitars | Core med behållare |
| Windows Server 2016 | 64-bitars |  Datacenter, kärna Datacenter, Datacenter med behållare |
| Windows Server 2012 R2 | 64-bitars |  Datacenter |
| Windows Server 2012 | 64-bitars |  Datacenter |
| Windows Server 2008 R2 SP1 | 64-bitars |  Datacenter |
| Windows Server 2008 SP2 | 64-bitars |  Ta med en egen avbildning |
| Windows 10 *(se anmärkning 1)* | 64-bitars Pro och Enterprise | Ta med en egen avbildning |

***Anmärkning 1:****för att distribuera Windows 10-klientoperativsystem på Azure-stacken, måste du ha [Windows licenser per användare](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) eller köpa genom en kvalificerad Multitenant-värden ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Marketplace-bilder är tillgängliga för lön-som-du-användning eller BYOL (EA/SPLA) licensiering. Användning av både på en enda Azure Stack-instans stöds inte. 

Datacenter-versionerna finns på marknaden; kunder kan sätta egna server-avbildningar inklusive andra versioner.

## <a name="linux"></a>Linux

Linux-distributioner i den här listan innehåller de nödvändiga Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Anpassade avbildningar ska skapas med den senaste versionen av offentliga WALA. Versioner som är äldre än 2.2.18 kanske inte fungerar på Azure-stacken.  
>
> [molnet init](https://cloud-init.io/) stöds inte på Azure-stacken just nu.

| Distribution | Beskrivning | Utgivare | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Behållaren Linux |  64-bitars | CoreOS | Stable |
| CentOS-baserade 6,9 | 64-bitars | Otillåten Wave | Ja |
| CentOS-baserade 7.4 | 64-bitars | Otillåten Wave | Ja |
| ClearLinux | 64-bitars | ClearLinux.org | Ja |
| Debian 8 ”Jessie” | 64-bitars | credativ |  Ja |
| Debian 9 ”Stretch” | 64-bitars | credativ | Ja |
| Red Hat Enterprise Linux 7.x (väntar) | 64-bitars | Red Hat |Ta med en egen avbildning |
| SLES 11SP4 | 64-bitars | SUSE | Ja |
| SLES 12SP3 | 64-bitars | SUSE | Ja |
| Ubuntu 14.04-LTS | 64-bitars | Canonical | Ja |
| Ubuntu 16.04-LTS | 64-bitars | Canonical | Ja |

Andra Linux-distributioner kanske stöds i framtiden.
