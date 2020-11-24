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
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563157"
---
Följande konfiguration användes för stegen nedan:

- Dator: Ubuntu Server 18,04
- Beroenden: strongSwan


Använd följande kommandon för att installera den nödvändiga strongSwan-konfigurationen:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Använd följande kommando för att installera Azures kommando rads gränssnitt:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Ytterligare anvisningar om hur du installerar Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)