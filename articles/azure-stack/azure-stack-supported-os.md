---
title: "Operativsystem som stöds för Azure-Stack | Microsoft Docs"
description: "Dessa gästoperativsystem kan användas på Azure-stacken."
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Gästoperativsystem som stöds på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="windows"></a>Windows
Azure stacken stöder Windows-gästoperativsystem som listas i följande tabell: bilder i Marketplace finns tillgängliga för nedladdning till Azure-stacken. Avbildningar för Windows-klienten är inte tillgängliga på marknaden.

Under distributionen, lägger Azure Stack in en lämplig version av gästagenten i bilden.

| Operativsystem | Beskrivning | Utgivare | OS-typ | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64-bitars | Microsoft | Windows | Datacenter |
| Windows Server 2012 | 64-bitars | Microsoft | Windows | Datacenter |
| Windows Server 2012 R2 | 64-bitars | Microsoft | Windows | Datacenter |
| Windows Server 2016 | 64-bitars | Microsoft | Windows | Datacenter, kärna Datacenter, Datacenter med behållare |
| Windows 10 *(se anmärkning 1)* | 64-bitars Pro och Enterprise | Microsoft | Windows | Nej |

***Anmärkning 1:****för att distribuera Windows 10-klientoperativsystem på Azure-stacken, måste du ha [Windows licenser per användare](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) eller köpa genom en kvalificerad Multitenant-värden ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).* 


## <a name="linux"></a>Linux

Linux-distributioner i den här listan innehåller de nödvändiga Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Bilder med WALA versioner äldre än 2.2.3 *inte* som stöds och inte är troligt att distribuera. Vissa versioner av WALA agent kallas inte fungerar på Azure-stacken virtuella datorer, inklusive versioner 2.2.12 och 2.2.13.
>
> [molnet init](https://cloud-init.io/) stöds bara på Ubuntu distributioner på Azure-stacken.

| Distribution | Beskrivning | Utgivare | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Behållaren Linux |  64-bitars | CoreOS | Stable |
| CentOS-based 6.9 | 64-bitars | Otillåten Wave | Ja |
| CentOS-baserade 7.4 | 64-bitars | Otillåten Wave | Ja |
| Debian 8 ”Jessie” | 64-bitars | credativ |  Ja |
| Debian 9 ”Stretch” | 64-bitars | credativ | Ja |
| Red Hat Enterprise Linux 7.x (väntar) | 64-bitars | Red Hat | Nej |
| SLES 11SP4 | 64-bitars | SUSE | Ja |
| SLES 12SP3 | 64-bitars | SUSE | Ja |
| Ubuntu 14.04-LTS | 64-bitars | Canonical | Ja |
| Ubuntu 16.04-LTS | 64-bitars | Canonical | Ja |

Andra Linux-distributioner kanske stöds i framtiden.
