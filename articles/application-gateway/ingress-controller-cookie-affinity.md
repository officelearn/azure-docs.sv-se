---
title: Aktivera cookie-baserad tillhörighet med Application Gateway
description: Den här artikeln innehåller information om hur du aktiverar cookie-baserad tillhörighet med en Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807960"
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
