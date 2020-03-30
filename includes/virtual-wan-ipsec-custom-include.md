---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168404"
---
Tänk på följande krav när du arbetar med anpassade IPsec-principer:

* **IKE** - För IKE kan du välja valfri parameter från IKE-kryptering, plus valfri parameter från IKE Integrity, plus valfri parameter från DH Group.
* **IPsec** - För IPsec kan du välja valfri parameter från IPsec-kryptering, plus valfri parameter från IPsec Integrity, plus PFS. Om någon av parametrarna för IPsec-kryptering eller IPsec-integritet är GCM måste parametrarna för båda inställningarna vara GCM.

>[!NOTE]
> Med anpassade IPsec-principer finns det inget begrepp för responder och initierare (till skillnad från Default IPsec-principer). Båda sidor (lokal och Azure VPN-gateway) använder samma inställningar för IKE fas 1 och IKE fas 2. Både IKEv1- och IKEv2-protokoll stöds. Det finns inget stöd för Azure som responder bara.
>

**Tillgängliga inställningar och parametrar**

| Inställning | Parametrar |
|--- |--- |
| IKE-kryptering | AES256, AES192, AES128 |
| IKE Integritet | SHA384, SHA256, SHA1 |
| DH-grupp | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPsec Integrity | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS-grupp | PFS24, ECP384, ECP256, PFS2048, PFS2 |
