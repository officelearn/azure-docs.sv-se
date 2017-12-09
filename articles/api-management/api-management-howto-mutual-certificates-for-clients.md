---
title: 'Skydda API: er med klienten certifikatautentisering i API Management - Azure API Management | Microsoft Docs'
description: "Lär dig hur du skyddar åtkomsten till API: er som använder klientcertifikat"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 841825923819bdb257e5b5983071d999cca805e9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Hur du skyddar API: er med klienten certifikatautentisering i API Management

API Management ger möjlighet att säker åtkomst till API: er (d.v.s. klient till API-hantering) använder klientcertifikat. För närvarande kan du kontrollera tumavtrycket för ett klientcertifikat mot ett önskat värde. Du kan också kontrollera tumavtrycket mot befintliga certifikat har överförts till API-hantering.  

Information om hur du skyddar åtkomsten till backend-tjänst för en API som använder klientcertifikat (d.v.s. API-hantering till backend-) finns [säkra backend-tjänster som använder klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>Kontroll av förfallodatum

Nedan principer kan konfigureras för att kontrollera om certifikatet har upphört att gälla:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Kontroll av utfärdare och ämne

Nedan principer kan konfigureras för att kontrollera utfärdare och ämnet för ett klientcertifikat:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Kontrollera certifikatets tumavtryck

Nedan principer kan konfigureras för att kontrollera tumavtrycket för ett klientcertifikat:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrollerar ett tumavtryck mot certifikat har överförts till API-hantering

I följande exempel visas hur du kontrollerar tumavtrycket för ett klientcertifikat mot certifikat har överförts till API-hantering: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Nästa steg

*  [Så här säkrar du backend-tjänster som använder klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Hur man överför certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

