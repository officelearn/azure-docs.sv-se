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
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992449"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurera identitet för Event Grid-modulen

Den här artikeln innehåller exempel på möjliga identitets konfigurationer för en Event Grid-modul. Som standard kommer Event Grid-modulen att presentera sitt identitets certifikat som det har kon figurer ATS av IoT Security daemon. Ett identitets certifikat visas av Event Grid-modulen på de utgående samtal som är när de levererar händelser. En prenumerant till en Event Grid-händelse kan sedan välja att verifiera att den verkligen är den Event Grid-modul som skickade händelsen innan händelsen accepteras.

Se [säkerhets-och autentiserings](security-authentication.md) guide för alla möjliga konfigurationer.

## <a name="always-present-identity-certificate"></a>Visa alltid identitets certifikat
Här är en exempel konfiguration för att alltid presentera ett identitets certifikat vid utgående samtal. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Visa inte identitets certifikat
Här är en exempel konfiguration för att inte presentera ett identitets certifikat vid utgående samtal. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
