---
title: Microsoft Azure-stacken Development Kit-arkitektur | Microsoft Docs
description: Visa arkitekturen i Microsoft Azure-stacken Development Kit.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b754ff5b5a82ac284eb59ff9b9d30a581f3d3af5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure-stacken Development Kit-arkitektur

*Gäller för: Azure stacken Development Kit*

Azure-stacken Development Kit är en nod distribution av Azure-stacken. Alla komponenter är installerade på virtuella datorer som körs på en enda värddator. 

## <a name="logical-architecture-diagram"></a>Diagram över logisk arkitektur
Följande diagram illustrerar logiska arkitekturen i Azure-stacken development kit och dess komponenter.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtuella datorroller
I Azure-stacken development kit erbjuder tjänster med hjälp av följande virtuella datorer på värden:

| Namn | Beskrivning |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack lagringstjänster.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Gräns-router och ger funktioner för Azure-stacken NAT och VPN. |
| **AzS-CA01** | Utfärdare-Certifikattjänster för Azure-stacken rolltjänster.|
| **AzS-DC01** | Active Directory, DNS och DHCP-tjänster för Microsoft Azure-stacken.|
| **AzS-ERCS01** | Återställning konsol VM. |
| **AzS-GWY01** | Gräns-gatewayen tjänster som plats-till-plats VPN-anslutningar för klientnätverk.|
| **AzS-NC01** | Nätverksstyrenheten, som hanterar Azure Stack nätverkstjänster.  |
| **AzS-SLB01** | Belastningsutjämning multiplexor tjänster i Azure-Stack för både klienter och Azure-stacken infrastrukturtjänster.  |
| **AzS-SQL01** | Internt datalager för infrastrukturroller på Azure-stacken.  |
| **AzS-WAS01** | Azure administrativa Stack-portalen och Azure Resource Manager-tjänster.|
| **AzS-WASP01**| Azure portal för stacken användare (klient) och Azure Resource Manager-tjänster.|
| **AzS-XRP01** | Infrastruktur för baskort för Microsoft Azure-stacken, inklusive resursproviders beräkning, nätverk och lagring.|


## <a name="next-steps"></a>Nästa steg
[Distribuera Azure Stack](azure-stack-deploy.md)

[Scenarier för första försök](azure-stack-first-scenarios.md)

