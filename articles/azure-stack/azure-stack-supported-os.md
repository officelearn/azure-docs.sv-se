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
ms.date: 01/18/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: c9f5bee38772623fb79fa081be8eaece981cc8ab
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Gästoperativsystem som stöds på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="windows"></a>Windows
Azure-stacken stöder följande Windows-gästoperativsystem. Bilder i Marketplace finns tillgängliga för nedladdning till Azure-stacken. Avbildningar för Windows-klienten är inte tillgängliga på marknaden.

Under distributionen kan garanterar Azure Stack att en lämplig version av gästagenten injekteras i bilden.

| Operativsystem | Beskrivning | Utgivare | OS-typ | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64-bitars | Microsoft | Windows | Datacenter |
| Windows Server 2012 | 64-bitars | Microsoft | Windows | Datacenter |
| Windows Server 2012 R2 | 64-bitars | Microsoft | Windows | Datacenter |
| Windows Server 2016 | 64-bitars | Microsoft | Windows | Datacenter, kärna Datacenter, Datacenter med behållare |
| Windows 7 | 64-bitars Pro och Enterprise | Microsoft | Windows | Nej |
| Windows 8.1 | 64-bitars Pro och Enterprise | Microsoft | Windows | Nej |
| Windows 10 *(se anmärkning 1)* | 64-bitars Pro och Enterprise | Microsoft | Windows | Nej |

***Anmärkning 1:****för att distribuera Windows 10-klientoperativsystem på Azure-stacken, måste du ha [Windows licenser per användare](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) eller köpa genom en kvalificerad Multitenant-värden ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).* 


## <a name="linux"></a>Linux

Linux-distributioner i den här listan innehåller de nödvändiga Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Bilder med WALA versioner äldre än 2.2.3 *inte* som stöds och inte är troligt att distribuera. Vissa versioner av WALA agent kallas inte fungerar på Azure-stacken virtuella datorer, inklusive versioner 2.2.12 och 2.2.13.


| Distribution | Beskrivning | Utgivare | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Behållaren Linux |  64-bitars | CoreOS | Stable |
| CentOS-based 6.9 | 64-bitars | Otillåten Wave | Ja |
| CentOS-baserad 7.3 | 64-bitars | Otillåten Wave | Ja |
| CentOS-baserade 7.4 | 64-bitars | Otillåten Wave | Ja |
| Debian 8 ”Jessie” | 64-bitars | credativ |  Ja |
| Debian 9 ”Stretch” | 64-bitars | credativ | Ja |
| Oracle Linux | 64-bitars | Oracle | Nej |
| Red Hat Enterprise Linux 7.x | 64-bitars | Red Hat | Nej |
| SLES 11SP4 | 64-bitars | SUSE | Ja |
| SLES 12SP3 | 64-bitars | SUSE | Ja |
| Ubuntu 14.04-LTS | 64-bitars | Canonical | Ja |
| Ubuntu 16.04-LTS | 64-bitars | Canonical | Ja |

Andra Linux-distributioner kanske stöds i framtiden.
