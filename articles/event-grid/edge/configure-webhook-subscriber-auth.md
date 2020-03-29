---
title: Konfigurera autentisering av webhook-prenumeranter – Azure Event Grid IoT Edge | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841738"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurera autentisering för webhook-prenumerant

Den här guiden ger exempel på möjliga webhook-prenumerantkonfigurationer för en Event Grid-modul. Som standard accepteras endast HTTPS-slutpunkter för webhook-prenumeranter. Event Grid-modulen avvisas om prenumeranten visar ett självsignerat certifikat.

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
>Ange `outbound__webhook__allowUnknownCA` egenskapen `true` till endast i testmiljöer eftersom du vanligtvis använder självsignerade certifikat. För produktionsarbetsbelastningar rekommenderar vi att de ställs in på **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Tillåt HTTPS-prenumerant men hoppa över certifikatvalidering

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
>Ange `outbound__webhook__skipServerCertValidation` egenskapen `true` till endast i testmiljöer eftersom du kanske inte presenterar ett certifikat som måste autentiseras. För produktionsarbetsbelastningar rekommenderar vi att de ställs in på **falska**

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
>Ange `outbound__webhook__httpsOnly` egenskapen `false` till endast i testmiljöer som du kanske vill visa en HTTP-prenumerant först. För produktionsarbetsbelastningar rekommenderar vi att de ställs in **på true**
