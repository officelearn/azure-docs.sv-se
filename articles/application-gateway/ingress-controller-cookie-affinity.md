---
title: Aktivera cookie-baserad tillhörighet med Application Gateway
description: Den här artikeln innehåller information om hur du aktiverar cookie-baserad tillhörighet med en Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795983"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Aktivera cookie-baserad tillhörighet med en programgateway
Som beskrivs i [Azure Application Gateway-dokumentationen](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)stöder Application Gateway cookie-baserad tillhörighet, vilket innebär att den kan dirigera efterföljande trafik från en användarsession till samma server för bearbetning.

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
