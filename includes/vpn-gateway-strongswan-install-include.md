---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ade787370278de147e446ebb12f0f06637fd8125
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035740"
---
Följande konfiguration användes för stegen nedan:

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE = Debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |Beroenden| strongSwan |


Använd följande kommandon för att installera den nödvändiga strongSwan-konfigurationen:

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```
