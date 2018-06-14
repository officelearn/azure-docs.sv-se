---
title: Azure Stack Development Kit-arkitektur | Microsoft Docs
description: Beskriver arkitekturen i Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975869"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure-stacken Development Kit-arkitektur
Azure Stack Development Kit (ASDK) är en nod distribution av Azure-stacken. Alla komponenter är installerade på virtuella datorer som körs på en enda värddator. 

## <a name="logical-architecture-diagram"></a>Diagram över logisk arkitektur
Följande diagram illustrerar logiska arkitekturen för ASDK och dess komponenter.

![ASDK arkitektur](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtuella datorroller
ASDK erbjuder tjänster med hjälp av följande virtuella datorer finns på värddatorn development kit:

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
[Lär dig mer om grundläggande ASDK administrationsuppgifter](asdk-admin-basics.md)
