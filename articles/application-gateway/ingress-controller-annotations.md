---
title: Anteckningar för ingående styrenhet för programgateway
description: Den här artikeln innehåller dokumentation om de anteckningar som är specifika för programgateway-ingress-styrenheten.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335825"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anteckningar för ingresskontroll för programgateway 

## <a name="introductions"></a>Introduktioner

Kubernetes ingress-resurs kan kommenteras med godtyckliga nyckel-/värdepar. AGIC förlitar sig på anteckningar för att programmera Application Gateway-funktioner, som inte kan konfigureras via Ingress YAML. Ingående anteckningar tillämpas på alla HTTP-inställningar, backend-pooler och lyssnare som härleds från en ingående resurs.

## <a name="list-of-supported-annotations"></a>Lista över anteckningar som stöds

För att en ingressresurs ska kunna observeras av `kubernetes.io/ingress.class: azure/application-gateway`AGIC måste den **kommenteras** med . Först då kommer AGIC att arbeta med ingressresursen i fråga.

| Anteckningsnyckel | Värdetyp | Standardvärde | Tillåtna värden
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(sekunder) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(sekunder) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefix för bakåtsträvningssökväg

Med den här anteckningen kan backend-sökvägen som anges i en ingående resurs skrivas om med prefixet som anges i den här anteckningen. På så sätt kan användare exponera tjänster vars slutpunkter skiljer sig från slutpunktsnamn som används för att exponera en tjänst i en ingående resurs.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
I exemplet ovan har vi definierat `go-server-ingress-bkprefix` en ingressresurs `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` med namnet med en anteckning . Anteckningen talar om för programgatewayen att skapa en HTTP-inställning, `/hello` `/test/`som har en åsidosättning av sökvägsprefixet för sökvägen till .

> [!NOTE] 
> I exemplet ovan har vi bara en definierad regel. Anteckningarna är dock tillämpliga på hela ingressresursen, så om en användare hade definierat flera regler skulle backend-sökvägens prefix ställas in för var och en av de angivna sökvägarna. Om en användare vill ha olika regler med olika sökvägsprefix (även för samma tjänst) måste de definiera olika ingressresurser.

## <a name="tls-redirect"></a>TLS-omdirigering

Application Gateway [kan konfigureras](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) för att automatiskt omdirigera HTTP-URL:er till sina HTTPS-motsvarigheter. När den här anteckningen finns och TLS är korrekt konfigurerad skapar Kubernetes Ingress-styrenhet en [routningsregel med en omdirigeringskonfiguration](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) och tillämpar ändringarna i programgatewayen. Den omdirigering som skapas är HTTP `301 Moved Permanently`.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Tömning av anslutning

`connection-draining`: Med den här anteckningen kan användare ange om anslutningen ska tömmas.
`connection-draining-timeout`: Med den här anteckningen kan användare ange en timeout varefter Application Gateway ska avsluta begäranden till den dränerande backend-slutpunkten.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie-baserad tillhörighet

Den här anteckningen gör det möjligt att ange om cookie-baserad tillhörighet ska aktiveras.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Timeout för begäran

Den här anteckningen gör det möjligt att ange tidsgränsen för begäran i sekunder efter vilken Application Gateway misslyckas med begäran om svaret inte tas emot.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Använd privat IP

Den här anteckningen gör det möjligt för oss att ange om den här slutpunkten ska exponeras på Private IP of Application Gateway.

> [!NOTE]
> * Application Gateway stöder inte flera IP-adresser på samma port (exempel: 80/443). Inträngning med anteckningar `appgw.ingress.kubernetes.io/use-private-ip: "false"` och `appgw.ingress.kubernetes.io/use-private-ip: "true"` en `HTTP` annan med på kommer att orsaka AGIC misslyckas med att uppdatera Application Gateway.
> * För Application Gateway som inte har en privat `appgw.ingress.kubernetes.io/use-private-ip: "true"` IP ignoreras Ingresses med. Detta återspeglas i styrenheten loggar och inträngning händelser `NoPrivateIP` för de ingresses med varning.


### <a name="usage"></a>Användning
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Exempel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Backend-protokoll

Denna anteckning tillåter oss att ange det protokoll som Application Gateway ska använda när du talar med poddar. Protokoll som `http`stöds: ,`https`

> [!NOTE]
> * Även om självsignerade certifikat stöds på Application Gateway, `https` stöder AGIC för närvarande bara när Pods använder certifikat som signerats av en välkänd certifikatutfärdare.
> * Se till att inte använda port 80 med HTTPS och port 443 med HTTP på poddar.

### <a name="usage"></a>Användning
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Exempel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```