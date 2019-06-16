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
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427333"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Hur du skyddar API: er med klienten certifikatautentisering i API Management

API Management ger möjlighet att säkra åtkomst till API: er (d.v.s. klient till API Management) med hjälp av klientcertifikat. Du kan validera inkommande certifikat och kontrollera certifikategenskaper mot önskade värden med hjälp av principuttryck.

Information om hur du skyddar åtkomst till backend-tjänst i ett API som använder klientcertifikat (t.ex, API Management till serverdelen) finns i [hur du skyddar backend-tjänster med hjälp av klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> För att ta emot och verifiera klientcertifikat på nivån för användning måste du först aktivera ”begäran klientcertifikat” inställningen på bladet ”anpassade domäner” enligt nedan.

![Klientcertifikat för begäran](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontroll av utfärdare och ämne

Nedan principer kan konfigureras för att kontrollera utfärdare och ämnet för ett klientcertifikat:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Inaktivera kontrollerar återkallade lista certifikatanvändningen `context.Request.Certificate.VerifyNoRevocation()` i stället för `context.Request.Certificate.Verify()`.
> Om klientcertifikatet är självsignerat rot (eller mellanliggande) CA-certifikat måste vara [överförs](api-management-howto-ca-certificates.md) till API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` ska fungera.

## <a name="checking-the-thumbprint"></a>Kontrollera tumavtrycket

Nedan principer kan konfigureras för att kontrollera tumavtrycket för ett klientcertifikat:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Inaktivera kontrollerar återkallade lista certifikatanvändningen `context.Request.Certificate.VerifyNoRevocation()` i stället för `context.Request.Certificate.Verify()`.
> Om klientcertifikatet är självsignerat rot (eller mellanliggande) CA-certifikat måste vara [överförs](api-management-howto-ca-certificates.md) till API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` ska fungera.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrollerar ett tumavtryck mot certifikat har överförts till API Management

I följande exempel visas hur du kontrollerar tumavtrycket för ett certifikat mot certifikat som har överförts till API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Inaktivera kontrollerar återkallade lista certifikatanvändningen `context.Request.Certificate.VerifyNoRevocation()` i stället för `context.Request.Certificate.Verify()`.
> Om klientcertifikatet är självsignerat rot (eller mellanliggande) CA-certifikat måste vara [överförs](api-management-howto-ca-certificates.md) till API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` ska fungera.

> [!TIP]
> Klientens certifikat deadlock problem som beskrivs i det här [artikeln](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) visar sig på flera olika sätt, t.ex. förfrågningar lås, begäranden resultatet `403 Forbidden` statuskod efter tidsgräns uppnås, `context.Request.Certificate` är `null`. Det här problemet påverkar vanligtvis `POST` och `PUT` begäranden med innehållslängd cirka 60 kB eller större.
> För att förhindra att aktivera det här problemet uppstår ”förhandla klientcertifikat” inställning för önskad värdnamn på bladet ”anpassade domäner” enligt nedan. Den här funktionen är inte tillgänglig på nivån för förbrukning.

![Förhandla klientcertifikat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Nästa steg

-   [Hur du skyddar backend-tjänster med hjälp av klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Ladda upp certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
