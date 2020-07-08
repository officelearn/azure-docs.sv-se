---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77111220"
---
| **Leverantör** | **Enhetsfamilj** | **Version av inbyggd program vara** |
| --- | --- | --- |
|Cisco | ISR| IOS 15,1 (för hands version)|
|Cisco | ASA | ASA (*) Routningsbaserad (IKEv2-inga BGP) för ASA under 9,8 |
|Cisco | ASA | ASA-Routningsbaserad (IKEv2-No BGP) för ASA 9.8 + |
|Juniper | SRX_GA | 12. x|
|Juniper | SSG_GA | Moarémönster 6.2. x|
|Juniper | JSeries_GA | JunOS 12. x|
|Juniper | SRX | JunOS 12. x Routningsbaserad BGP |
|Ubiquiti| EdgeRouter| Randering v 1.10 x Routningsbaserad VTI|
|Ubiquiti| EdgeRouter| Randering v 1.10 x Routningsbaserad BGP|

> [!NOTE]
> ( * ) Krävs: NarrowAzureTrafficSelectors (aktivera UsePolicyBasedTrafficSelectors-alternativet) och CustomAzurePolicies (IKE/IPsec)
>
