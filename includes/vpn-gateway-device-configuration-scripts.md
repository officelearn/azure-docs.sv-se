---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 00889717e0c22477b9933725bccc7de05c82bc4f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454435"
---
| **Leverantör** | **Enhetsfamilj** | **Version av inbyggd programvara** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (förhandsversion)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2-No BGP) för ASA nedan 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - utan BGP) för ASA 9.8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Krävs: NarrowAzureTrafficSelectors (aktivera UsePolicyBasedTrafficSelectors alternativet) och CustomAzurePolicies (IPsec/IKE)
>
