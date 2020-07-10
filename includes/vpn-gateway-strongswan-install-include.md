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
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86218060"
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

[Ytterligare anvisningar om hur du installerar Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
