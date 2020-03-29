---
title: Säkra API:er med klientcertifikatautentisering i API Management
titleSuffix: Azure API Management
description: Lär dig hur du skyddar åtkomsten till API:er med klientcertifikat
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713143"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Så skyddar du API:er genom att autentisera klientcertifikat i API Management

API Management ger möjlighet att skydda åtkomst till API:er (t.ex. klient till API Management) med hjälp av klientcertifikat. Du kan validera inkommande certifikat och kontrollera certifikategenskaper mot önskade värden med hjälp av principuttryck.

Information om hur du skyddar åtkomsten till serverdelstjänsten för ett API med klientcertifikat (dvs. API-hantering till serverdelstjänst) finns i [Så här skyddar du backend-tjänster med klientcertifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Om du vill ta emot och verifiera klientcertifikat via HTTP/2 på nivåerna Utvecklare, Grundläggande, Standard eller Premium måste du aktivera inställningen Förhandla om klientcertifikat på bladet "Anpassade domäner" enligt nedan.

![Förhandla om klientcertifikat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Om du vill ta emot och verifiera klientcertifikat på förbrukningsnivån måste du aktivera inställningen "Begär klientcertifikat" på bladet "Anpassade domäner" som visas nedan.

![Begär klientcertifikat](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontroll av emittenten och ämnet

Nedan kan principer konfigureras för att kontrollera utfärdaren och föremål för ett klientcertifikat:

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
> Så här inaktiverar du `context.Request.Certificate.VerifyNoRevocation()` användning `context.Request.Certificate.Verify()`av listan över återkallade certifikat i stället för .
> Om klientcertifikatet är självsignerat måste rotcertifikat (eller mellanliggande certifikatutfärdare) [överföras](api-management-howto-ca-certificates.md) till API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` för att fungera.

## <a name="checking-the-thumbprint"></a>Kontrollera tumavtrycket

Nedan kan principer konfigureras för att kontrollera tumavtrycket för ett klientcertifikat:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Så här inaktiverar du `context.Request.Certificate.VerifyNoRevocation()` användning `context.Request.Certificate.Verify()`av listan över återkallade certifikat i stället för .
> Om klientcertifikatet är självsignerat måste rotcertifikat (eller mellanliggande certifikatutfärdare) [överföras](api-management-howto-ca-certificates.md) till API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` för att fungera.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrollera ett tumavtryck mot certifikat som överförts till API Management

I följande exempel visas hur du kontrollerar tumavtrycket för ett klientcertifikat mot certifikat som överförts till API Management:

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
> Så här inaktiverar du `context.Request.Certificate.VerifyNoRevocation()` användning `context.Request.Certificate.Verify()`av listan över återkallade certifikat i stället för .
> Om klientcertifikatet är självsignerat måste rotcertifikat (eller mellanliggande certifikatutfärdare) [överföras](api-management-howto-ca-certificates.md) till API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` för att fungera.

> [!TIP]
> Fellåsproblem för klientcertifikat som beskrivs i den här [artikeln](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) kan visa `403 Forbidden` sig på flera `context.Request.Certificate` sätt, `null`t.ex. Det här `POST` problemet `PUT` påverkar vanligtvis och begäranden med innehållslängd på cirka 60 000 kronor eller större.
> För att förhindra att det här problemet uppstår aktiverar du inställningen Förhandla om klientcertifikat för önskade värdnamn på bladet "Anpassade domäner" enligt nedan. Den här funktionen är inte tillgänglig på förbrukningsnivån.

![Förhandla om klientcertifikat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Nästa steg

-   [Så här skyddar du backend-tjänster med klientcertifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Så här laddar du upp certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
