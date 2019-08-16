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
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520886"
---
Följande konfiguration användes för stegen nedan:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Beroenden| strongSwan |


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
