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
ms.openlocfilehash: f2d7aba05fc01c5a4dcdb123f25242c4e4a72578
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "43426444"
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
