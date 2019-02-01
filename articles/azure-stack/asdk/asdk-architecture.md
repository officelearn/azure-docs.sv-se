---
title: Azure Stack Development Kit-arkitektur | Microsoft Docs
description: Här beskrivs arkitekturen för Azure Stack Development Kit (ASDK).
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 2cee4d75a111f56012782228d8070bb728720aca
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238356"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit-arkitektur
Azure Stack Development Kit (ASDK) är en nod-distribution av Azure Stack. Alla komponenter är installerade på virtuella datorer som körs på en enda värddator. 

## <a name="logical-architecture-diagram"></a>Diagram över logisk arkitektur
Följande diagram illustrerar logisk arkitektur för ASDK och dess komponenter.

![ASDK arkitektur](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Roller för virtuella datorer
ASDK erbjuder tjänster med hjälp av följande virtuella datorer finns på värddatorn för development kit:

| Name | Beskrivning |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack-lagringstjänster.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Edge-router och ger funktioner för Azure Stack NAT och VPN. |
| **AzS-CA01** | Utfärdare av Certifikattjänster för Azure Stack-rolltjänster.|
| **AzS-DC01** | Active Directory, DNS och DHCP-tjänster för Microsoft Azure Stack.|
| **AzS-ERCS01** | Vid akutfall Återställningskonsolen VM. |
| **AzS-GWY01** | Edge-gateway-tjänster till exempel VPN plats-till-plats-anslutningar för klientnätverk.|
| **AzS-NC01** | Nätverksstyrenheten, som hanterar Azure Stack-nätverkstjänster.  |
| **AzS-SLB01** | Belastningsutjämning multiplexor tjänster i Azure Stack för både klienter och Azure Stack-infrastrukturtjänster.  |
| **AzS-SQL01** | Internt datalager för roller för Azure Stack-infrastruktur.  |
| **AzS-WAS01** | Azure Stack administrativ portal och Azure Resource Manager-tjänster.|
| **AzS-WASP01**| Azure Stack (klient) användarportalen och Azure Resource Manager-tjänster.|
| **AzS-XRP01** | Infrastruktur för baskort för Microsoft Azure Stack, inklusive Compute, Network och Storage-resursprovidrar.|


## <a name="next-steps"></a>Nästa steg
[Lär dig mer om grundläggande ASDK administrationsuppgifter](asdk-admin-basics.md)
