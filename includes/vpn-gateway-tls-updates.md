---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147074"
---
>[!NOTE]
>Från och med 1 juli 2018 tas stödet för TLS 1.0 och 1.1 bort från Azure VPN Gateway. VPN Gateway kommer endast att stödja TLS 1.2. För att underhålla support kan se den [uppdateringar att aktivera stöd för TLS1.2](#tls1).

Dessutom följande äldre algoritmer kommer också att bli inaktuella för TLS 1 juli 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm, datakrypteringsalgoritm)
* 3DES (Triple Data Encryption Algorithm, tredubbel datakrypteringsalgoritm)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Hur gör jag för att aktivera stöd för TLS 1.2 i Windows 7 och Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
