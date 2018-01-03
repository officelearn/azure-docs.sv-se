---
title: "Gästen operativsystems stöd för Azure-stacken | Microsoft Docs"
description: "Dessa gästoperativsystem kan användas på Azure-stacken."
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGoldner
ms.openlocfilehash: af524b5f40fb79648d91deb54186826902aa8cd2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
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
| Windows 10 | 64-bitars Pro och Enterprise | Microsoft | Windows | Nej |

## <a name="linux"></a>Linux

Linux-distributioner i den här listan innehåller de nödvändiga Windows Azure Linux Agent (WALA). 

> [!NOTE]
> Bilder med WALA versioner äldre än 2.2.3 *inte* som stöds och inte är troligt att distribuera.

| Distribution | Beskrivning | Utgivare | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Behållaren Linux |  64-bitars | CoreOS | Stable |
| CentOS-baserade 6,9 | 64-bitars | Otillåten Wave | Ja |
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




