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
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520886"
---
Följande konfiguration användes för stegen nedan:

  | | |
  |---|---|
  |Dator| Ubuntu Server 18.04|
  |Beroenden| Strongswan |


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

Använd följande kommando för att installera Azure-kommandoradsgränssnittet:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Ytterligare instruktioner om hur du installerar Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
