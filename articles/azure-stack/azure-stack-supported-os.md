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
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 8d9337053c8905886ed4429d64f8ef5b4e2c7d14
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060455"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Gästoperativsystem som stöds på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="windows"></a>Windows

Azure-stacken stöder Windows-gästoperativsystem som anges i följande tabell:

| Operativsystem | Beskrivning | Tillgänglig i Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, version 1709 | 64-bitars | Core med behållare |
| Windows Server 2016 | 64-bitars |  Datacenter, kärna Datacenter, Datacenter med behållare |
| Windows Server 2012 R2 | 64-bitars |  Datacenter |
| Windows Server 2012 | 64-bitars |  Datacenter |
| Windows Server 2008 R2 SP1 | 64-bitars |  Datacenter |
| Windows Server 2008 SP2 | 64-bitars |  Ta med en egen avbildning |
| Windows 10 *(se anmärkning 1)* | 64-bitars Pro och Enterprise | Ta med en egen avbildning |

***Anmärkning 1:*** *för att distribuera Windows 10-klientoperativsystem på Azure-stacken, måste du ha [Windows licenser per användare](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) eller köpa genom en kvalificerad Multitenant-värden ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

Marketplace-bilder är tillgängliga för lön-som-du-användning eller BYOL (EA/SPLA) licensiering. Användning av både på en enda Azure Stack-instans stöds inte. Under distributionen, lägger Azure Stack in en lämplig version av gästagenten i bilden.

 Datacenter-versioner är tillgängliga på marknaden för att ladda ned; kunder kan sätta egna server-avbildningar inklusive andra versioner. Avbildningar för Windows-klienten är inte tillgängliga på marknaden.

## <a name="linux"></a>Linux

Linux-distributioner visas som tillgängliga i Marketplace innehåller den nödvändiga Windows Azure Linux Agent (WALA). Om du använder en egen avbildning till Azure-stacken följa riktlinjerna i [lägger till Linux-avbildningar till Azure-stacken](azure-stack-linux.md).

> [!NOTE]
> Anpassade avbildningar ska skapas med den senaste versionen av offentliga WALA. Versioner som är äldre än 2.2.18 kanske inte fungerar på Azure-stacken.
>
> [molnet init](https://cloud-init.io/) stöds inte på Azure-stacken just nu.

| Distribution | Beskrivning | Utgivare | Marketplace |
| --- | --- | --- | --- | --- | --- |
| CentOS-baserade 6,9 | 64-bitars | Otillåten Wave | Ja |
| CentOS-baserade 7.4 | 64-bitars | Otillåten Wave | Ja |
| ClearLinux | 64-bitars | ClearLinux.org | Ja |
| Behållaren Linux |  64-bitars | CoreOS | Stable |
| Debian 8 ”Jessie” | 64-bitars | credativ |  Ja |
| Debian 9 ”Stretch” | 64-bitars | credativ | Ja |
| Red Hat Enterprise Linux 7.x | 64-bitars | Red Hat |Ta med en egen avbildning |
| SLES 11SP4 | 64-bitars | SUSE | Ja |
| SLES 12SP3 | 64-bitars | SUSE | Ja |
| Ubuntu 14.04-LTS | 64-bitars | Canonical | Ja |
| Ubuntu 16.04-LTS | 64-bitars | Canonical | Ja |
| Ubuntu 18.04-LTS | 64-bitars | Canonical | Ja |

Andra Linux-distributioner kanske stöds i framtiden.

Information för Red Hat Enterprise Linux-support, referera till [Red Hat och Azure-stacken: vanliga frågor och svar](https://access.redhat.com/articles/3413531).
