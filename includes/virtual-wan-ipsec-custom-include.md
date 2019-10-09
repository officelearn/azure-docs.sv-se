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
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168404"
---
Tänk på följande när du arbetar med anpassade IPsec-principer:

* **IKE** – för IKE kan du välja vilken parameter som helst från IKE-kryptering, plus vilken parameter som helst från IKE-integritet, samt vilken parameter som helst från DH-gruppen.
* **IPSec** -för IPSec kan du välja valfri parameter från IPSec-kryptering, plus vilken parameter som helst från IPSec-integritet, plus PFS. Om någon av parametrarna för IPsec-kryptering eller IPsec-integritet är GCM måste parametrarna för båda inställningarna vara GCM.

>[!NOTE]
> Med anpassade IPsec-principer finns det inget koncept för responder och Initiator (till skillnad från IPsec-standardprinciper). Både sidor (lokala och Azure VPN-gateway) kommer att använda samma inställningar för IKE fas 1 och IKE fas 2. Både IKEv1-och IKEv2-protokoll stöds. Det finns inget stöd för Azure som en responder.
>

**Tillgängliga inställningar och parametrar**

| Inställning | Parametrar |
|--- |--- |
| IKE-kryptering | AES256, AES192, AES128 |
| IKE-integritet | SHA384, SHA256, SHA1 |
| DH-grupp | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPsec Integrity | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS-grupp | PFS24, ECP384, ECP256, PFS2048, PFS2 |
