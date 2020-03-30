---
title: Exponera en WebSocket-server för Application Gateway
description: Den här artikeln innehåller information om hur du exponerar en WebSocket-server för Application Gateway med ingressstyrenhet för AKS-kluster.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297840"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Exponera en WebSocket-server för Application Gateway

Som beskrivs i Application Gateway v2-dokumentationen - det [ger inbyggt stöd för WebSocket och HTTP/2-protokoll](features.md#websocket-and-http2-traffic). Observera att för både Application Gateway och Kubernetes Ingress - det finns ingen användarkonfigurerbar inställning för att selektivt aktivera eller inaktivera WebSocket-stöd.

Yaml för Kubernetes-distribution nedan visar den minsta konfiguration som används för att distribuera en WebSocket-server, vilket är samma sak som att distribuera en vanlig webbserver:
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

Med tanke på att alla förutsättningar uppfylls och du har en Application Gateway som styrs av en Kubernetes Ingress i din AKS, skulle distributionen ovan `ws.contoso.com` resultera i en WebSockets-server som exponeras på port 80 i application gateways offentliga IP och domänen.

Följande cURL-kommando testar WebSocket-serverdistributionen:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket Hälsa Sonder

Om distributionen inte uttryckligen definierar hälsoavsökningar försöker Application Gateway ha en HTTP GET på webSocket-serverns slutpunkt.
Beroende på serverns implementering[(här är en vi älskar)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)webSocket specifika rubriker kan krävas (till`Sec-Websocket-Version` exempel).
Eftersom Application Gateway inte lägger till WebSocket-huvuden kommer Application Gateways hälsoavsökningssvar `400 Bad Request`från WebSocket-servern troligen att vara .
Som ett resultat Application Gateway kommer att markera dina poddar `502 Bad Gateway` som ohälsosamma, vilket så småningom kommer att resultera i en för konsumenterna av WebSocket servern.
För att undvika detta kan du behöva lägga till en HTTP`/health` GET-hanterare `200 OK`för en hälsokontroll till servern (till exempel, som returnerar).
