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
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c69b124f84e87e8f0b937dfa275378c376894f9b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447208"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit-arkitektur
Azure Stack Development Kit (ASDK) är en nod-distribution av Azure Stack som körs på en enda värddator. Edge routingkomponenter är installerade på värddatorn för att lägga till NAT- och VPN-funktioner för Azure Stack. Azure Stack-infrastruktur-roller som körs i Hyper-V-lager av den fysiska värddatorn.


## <a name="virtual-machine-roles"></a>Roller för virtuella datorer
ASDK erbjuder tjänster med hjälp av följande virtuella datorer finns på värddatorn för development kit:

| Namn | Beskrivning |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack-lagringstjänster.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
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
