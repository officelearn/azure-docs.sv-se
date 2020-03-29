---
title: Konfigurera identitet – Azure Event Grid IoT Edge | Microsoft-dokument
description: Konfigurera eventrutnätsmodulens identitet
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841773"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurera identitet för modulen Event Grid

Den här artikeln innehåller hur du konfigurerar identitet för Rutnät på kant. Som standard visar modulen Event Grid sitt identitetscertifikat som konfigurerats av IoT-säkerhetsdemonen. Event Grid on Edge presenterar sitt identitetscertifikat med sina utgående anrop när det levererar händelser. En prenumerant kan sedan validera det är modulen Event Grid som skickade händelsen innan den accepterade.

Se [säkerhets- och autentiseringsguiden](security-authentication.md) för alla möjliga konfigurationer.

## <a name="always-present-identity-certificate"></a>Visa alltid identitetscertifikat
Här är en exempelkonfiguration för att alltid presentera ett identitetscertifikat för utgående samtal. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Visa inte identitetscertifikat
Här är en exempelkonfiguration för att inte presentera ett identitetscertifikat för utgående samtal. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
