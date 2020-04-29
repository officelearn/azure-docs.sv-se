---
title: Konfigurera autentisering med webhook-prenumerant – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurera autentisering för webhook-prenumerant
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841738"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurera autentisering för webhook-prenumerant

Den här guiden innehåller exempel på möjliga webhook-Subscriber-konfigurationer för en Event Grid-modul. Som standard accepteras bara HTTPS-slutpunkter för webhook-prenumeranter. Event Grid-modulen kommer att nekas om prenumeranten presenterar ett självsignerat certifikat.

## <a name="allow-only-https-subscriber"></a>Tillåt endast HTTPS-prenumerant

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Tillåt HTTPS-prenumerant med självsignerat certifikat

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound__webhook__allowUnknownCA` till `true` endast i test miljöer som du vanligt vis använder självsignerade certifikat. För produktions arbets belastningar rekommenderar vi att de anges till **falskt**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Tillåt HTTPS-prenumerant men hoppa över certifikat verifiering

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound__webhook__skipServerCertValidation` till `true` endast i test miljöer eftersom du kanske inte presenterar ett certifikat som behöver autentiseras. För produktions arbets belastningar rekommenderar vi att de anges till **falskt**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Tillåt både HTTP och HTTPS med självsignerade certifikat

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ange egenskapen `outbound__webhook__httpsOnly` som `false` endast i test miljöer som du kanske vill ha en http-prenumerant först. För produktions arbets belastningar rekommenderar vi att de anges till **Sant**
