---
title: Konfigurera klientautentisering av inkommande anrop – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurera API-protokoll som exponeras av Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992514"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurera klientautentisering av inkommande samtal

Den här guiden innehåller exempel på konfigurationer av möjliga klientautentisering för Event Grid-modulen. Event Grid-modulen stöder två typer av klientautentisering:-

* Signaturer för delad åtkomst (SAS)
* certifikatbaserad

Se [säkerhets-och autentiserings](security-authentication.md) guide för alla möjliga konfigurationer.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Aktivera certifikatbaserad klientautentisering, inga självsignerade certifikat

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Aktivera certifikatbaserad klientautentisering, Tillåt självsignerade certifikat

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Ange egenskapen **inkommande: clientAuth: clientCert: allowUnknownCA** till **True** endast i test miljöer som du vanligt vis använder självsignerade certifikat. För produktions arbets belastningar rekommenderar vi att du ställer in den här egenskapen på **false** och certifikat från en certifikat utfärdare (ca).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Aktivera certifikatbaserad autentisering och SAS-baserad klientautentisering

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS-baserad klientautentisering tillåter en icke-IoT Edge-modul för hanterings-och körnings åtgärder som förutsätter att API-portarna är tillgängliga utanför IoT Edge nätverket.
