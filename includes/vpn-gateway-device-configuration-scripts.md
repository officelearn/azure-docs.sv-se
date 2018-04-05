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
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
| **Leverantör** | **Enhetsfamilj** | **Version på inbyggd programvara** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (förhandsgranskning)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2-No BGP) för ASA nedan 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - ingen BGP) för ASA 9.8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Krävs: NarrowAzureTrafficSelectors och CustomAzurePolicies (IKE/IPsec)
>