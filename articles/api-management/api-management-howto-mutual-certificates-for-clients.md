---
title: 'Skydda API: er med klienten certifikatautentisering i API Management – Azure API Management | Microsoft Docs'
description: 'Lär dig hur du skyddar åtkomsten till API: er med klientcertifikat'
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 3307ea391734828cb83c927e8df8aca79685279a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441544"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Hur du skyddar API: er med klienten certifikatautentisering i API Management

API Management ger möjlighet att säkra åtkomst till API: er (d.v.s. klient till API Management) med hjälp av klientcertifikat. För närvarande kan du kontrollera tumavtrycket för ett certifikat mot ett önskat värde. Du kan också kontrollera tumavtrycket mot befintliga certifikat som har överförts till API Management.  

Information om hur du skyddar åtkomst till backend-tjänst i ett API som använder klientcertifikat (t.ex, API Management till backend-server) finns i [hur du skyddar backend-tjänster med hjälp av klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>Kontrollera utgångsdatumet

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

## <a name="checking-the-thumbprint"></a>Kontrollera tumavtrycket

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

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrollerar ett tumavtryck mot certifikat har överförts till API Management

I följande exempel visas hur du kontrollerar tumavtrycket för ett certifikat mot certifikat som har överförts till API Management: 

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

*  [Hur du skyddar backend-tjänster med hjälp av klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Ladda upp certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

