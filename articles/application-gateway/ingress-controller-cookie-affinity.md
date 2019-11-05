---
title: Aktivera cookie-baserad tillhörighet med Application Gateway
description: Den här artikeln innehåller information om hur du aktiverar cookie-baserad tillhörighet med en Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513580"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Aktivera cookie-baserad tillhörighet med en Application Gateway
Som beskrivs i [Azure Application Gateway-dokumentationen](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)Application Gateway stöder cookie-baserad tillhörighet, vilket innebär att den kan dirigera efterföljande trafik från en användarsession till samma server för bearbetning.

## <a name="example"></a>Exempel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
