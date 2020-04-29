---
title: Exponera en WebSocket-Server för Application Gateway
description: Den här artikeln innehåller information om hur du exponerar en WebSocket-Server för att Application Gateway med ingångs styrenheten för AKS-kluster.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297840"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Exponera en WebSocket-Server för Application Gateway

Som beskrivs i dokumentationen för Application Gateway v2 – den [innehåller inbyggt stöd för WebSocket-och http/2-protokollen](features.md#websocket-and-http2-traffic). Observera att för både Application Gateway och Kubernetes ingress – det finns ingen användar konfigurerbar inställning för att selektivt aktivera eller inaktivera WebSocket-stöd.

I Kubernetes Deployment YAML nedan visas den minsta konfiguration som används för att distribuera en WebSocket-Server, vilket är samma som att distribuera en vanlig webb server:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Eftersom alla krav är uppfyllda och du har en Application Gateway som styrs av en Kubernetes ingress i din AKS, skulle distributionen ovan leda till att en WebSockets-Server exponeras på port 80 i Application Gateways offentliga IP och `ws.contoso.com` domänen.

Följande spiral-kommando testar distributionen av WebSocket-servern:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket Health-avsökningar

Om distributionen inte uttryckligen definierar hälso avsökningar försöker Application Gateway ett HTTP-fel på WebSocket-serverns slut punkt.
Beroende på Server implementeringen ([här är ett vi älskar](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) kan WebSocket-speciella huvuden krävas (`Sec-Websocket-Version` till exempel).
Eftersom Application Gateway inte lägger till WebSocket-huvuden, är Application Gateway s hälso avsöknings svar från din WebSocket-Server `400 Bad Request`förmodligen troligt vis.
Som ett resultat Application Gateway Markera din poddar som ohälsosam, vilket kommer att leda till en `502 Bad Gateway` för användare av WebSocket-servern.
För att undvika detta kan du behöva lägga till en HTTP GET-hanterare för en hälso kontroll på servern`/health` (till exempel, som `200 OK`returnerar).
