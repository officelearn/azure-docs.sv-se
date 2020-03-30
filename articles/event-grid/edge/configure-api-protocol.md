---
title: Konfigurera API-protokoll – Azure Event Grid IoT Edge | Microsoft-dokument
description: Konfigurera API-protokoll som exponeras av händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841818"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurera API-protokoll för händelserutnät

Den här guiden ger exempel på möjliga protokollkonfigurationer för en Event Grid-modul. Modulen Event Grid exponerar API för dess hanterings- och körningsåtgärder. I följande tabell fångas protokoll och portar.

| Protokoll | Port | Beskrivning |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Inaktiverad som standard. Användbart endast under testning. Inte lämplig för produktionsarbetsbelastningar.
| HTTPS | 4438 | Default

Se [säkerhets- och autentiseringsguiden](security-authentication.md) för alla möjliga konfigurationer.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Exponera HTTPS för IoT-moduler i samma kantnätverk

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Aktivera HTTPS till andra IoT-moduler och icke-IoT-arbetsbelastningar

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> I avsnittet **PortBindings** kan du mappa interna portar till portar för behållarvärden. Den här funktionen gör det möjligt att nå modulen Event Grid utanför IoT Edge-behållarnätverket, om IoT-kantenheten kan nås offentligt.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Exponera HTTP- och HTTPS-moduler för IoT-moduler i samma edge-nätverk

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Aktivera HTTP- och HTTPS-moduler till andra IoT-moduler och icke-IoT-arbetsbelastningar

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Som standard är varje IoT-modul en del av IoT Edge-körningen som skapas av bryggnätverket. Det gör det möjligt för olika IoT-moduler i samma nätverk att kommunicera med varandra. **PortBindings** kan du kartlägga en container intern port på värdmaskinen så att vem som helst kan komma åt Event Grid modulens port utifrån.

>[!IMPORTANT]
> Även om portarna kan göras tillgängliga utanför IoT Edge-nätverket, tvingar klientautentisering vem som faktiskt tillåts ringa in anrop till modulen.
