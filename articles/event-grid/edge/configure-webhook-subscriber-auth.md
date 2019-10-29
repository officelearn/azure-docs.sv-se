---
title: Konfigurera autentisering med webhook-prenumerant – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurera autentisering med webhook-prenumerant
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992488"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurera autentisering med webhook-prenumerant

Den här guiden innehåller exempel på möjliga webhook-Subscriber-konfigurationer för en Event Grid-modul. Som standard accepteras bara HTTPS-slutpunkter för webhook-prenumeranter. Event Grid-modulen kommer att nekas om prenumeranten presenterar ett självsignerat certifikat.

## <a name="allow-only-https-subscriber"></a>Tillåt endast HTTPS-prenumerant

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Tillåt HTTPS-prenumerant med självsignerat certifikat

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound:webhook:allowUnknownCA` `true` endast i test miljöer som du vanligt vis använder självsignerade certifikat. För produktions arbets belastningar rekommenderar vi att de anges till **falskt**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Tillåt HTTPS-prenumerant men hoppa över certifikat verifiering

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound:webhook:skipServerCertValidation` att `true` endast i test miljöer eftersom du kanske inte presenterar ett certifikat som behöver autentiseras. För produktions arbets belastningar rekommenderar vi att de anges till **falskt**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Tillåt både HTTP och HTTPS med självsignerade certifikat

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ställ in egenskapen `outbound:webhook:httpsOnly` till att `false` endast i test miljöer som du kanske vill ha en HTTP-prenumerant först. För produktions arbets belastningar rekommenderar vi att de anges till **Sant**
