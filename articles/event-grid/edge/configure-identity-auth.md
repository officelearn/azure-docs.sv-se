---
title: Konfigurera identitets Azure Event Grids IoT Edge | Microsoft Docs
description: Konfigurera Event Grid modulens identitet
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841773"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurera identitet för Event Grid-modulen

Den här artikeln beskriver hur du konfigurerar identitet för rutnätet på kant. Som standard visar Event Grid-modulen sitt identitets certifikat enligt konfigurationen i IoT Security daemon. Event Grid på gränsen visar sitt identitets certifikat med utgående samtal när de levererar händelser. En prenumerant kan sedan verifiera att den är den Event Grid-modul som skickade händelsen innan den accepteras.

Se [säkerhets-och autentiserings](security-authentication.md) guide för alla möjliga konfigurationer.

## <a name="always-present-identity-certificate"></a>Visa alltid identitets certifikat
Här är en exempel konfiguration för att alltid presentera ett identitets certifikat vid utgående samtal. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Visa inte identitets certifikat
Här är en exempel konfiguration för att inte presentera ett identitets certifikat vid utgående samtal. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
