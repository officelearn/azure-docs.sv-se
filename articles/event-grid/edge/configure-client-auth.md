---
title: Konfigurera klientautentisering av inkommande samtal - Azure Event Grid IoT Edge | Microsoft-dokument
description: Konfigurera API-protokoll som exponeras av händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841799"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurera klientautentisering av inkommande samtal

Den här guiden ger exempel på möjliga klientautentiseringskonfigurationer för modulen Event Grid. Event Grid-modulen stöder två typer av klientautentisering:

* SAS-signatur (Shared Access Signature)
* Certifikatbaserad

Se [säkerhets- och autentiseringsguiden](security-authentication.md) för alla möjliga konfigurationer.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Aktivera certifikatbaserad klientautentisering, inga självsignerade certifikat

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Aktivera certifikatbaserad klientautentisering, tillåt självsignerade certifikat

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Ange att egenskapen **inbound__clientAuth__clientCert__allowUnknownCA** bara **är true** i testmiljöer eftersom du vanligtvis använder självsignerade certifikat. För produktionsarbetsbelastningar rekommenderar vi att du anger den här egenskapen till **false** och certifikat från en certifikatutfärdare.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Aktivera certifikatbaserad och sas-nyckelbaserad klientautentisering

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS nyckelbaserade klientautentisering gör det möjligt för en icke-IoT-kantmodul att utföra hanterings- och körningsåtgärder förutsatt att API-portarna är tillgängliga utanför IoT Edge-nätverket.
