---
title: 'Säkra API: er med autentisering av klient certifikat i API Management-Azure API Management | Microsoft Docs'
description: 'Lär dig hur du skyddar åtkomsten till API: er med klient certifikat'
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072365"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Skydda API: er med autentisering av klient certifikat i API Management

API Management ger möjlighet att skydda åtkomsten till API: er (t. ex. klient som API Management) med klient certifikat. Du kan verifiera inkommande certifikat och kontrol lera certifikat egenskaperna mot önskade värden med hjälp av princip uttryck.

Information om hur du skyddar åtkomst till backend-tjänsten för ett API med hjälp av klient certifikat (d.v.s. API Management till Server del) finns i [så här skyddar du backend-tjänster med autentisering av klient certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Om du vill ta emot och verifiera klient certifikat i förbruknings nivån måste du först aktivera inställningen "begära klient certifikat" på bladet "anpassade domäner" enligt nedan.

![Begär klient certifikat](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontrollerar utfärdaren och ämnet

Principer för nedan kan konfigureras för att kontrol lera utfärdaren och ämnet för ett klient certifikat:

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
> Om du vill inaktivera kontrollen av listan över `context.Request.Certificate.VerifyNoRevocation()` återkallade certifikat använder du i stället för. `context.Request.Certificate.Verify()`
> Om klient certifikatet är självsignerat måste rot-eller mellanliggande certifikat utfärdare överföras till [](api-management-howto-ca-certificates.md) API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` för att fungera.

## <a name="checking-the-thumbprint"></a>Kontrollerar tumavtrycket

Principer för nedan kan konfigureras för att kontrol lera tumavtrycket för ett klient certifikat:

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
> Om du vill inaktivera kontrollen av listan över `context.Request.Certificate.VerifyNoRevocation()` återkallade certifikat använder du i stället för. `context.Request.Certificate.Verify()`
> Om klient certifikatet är självsignerat måste rot-eller mellanliggande certifikat utfärdare överföras till [](api-management-howto-ca-certificates.md) API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` för att fungera.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrol lera ett tumavtryck mot certifikat som har överförts till API Management

I följande exempel visas hur du kontrollerar tumavtrycket för ett klient certifikat mot certifikat som har överförts till API Management:

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
> Om du vill inaktivera kontrollen av listan över `context.Request.Certificate.VerifyNoRevocation()` återkallade certifikat använder du i stället för. `context.Request.Certificate.Verify()`
> Om klient certifikatet är självsignerat måste rot-eller mellanliggande certifikat utfärdare överföras till [](api-management-howto-ca-certificates.md) API Management för `context.Request.Certificate.Verify()` och `context.Request.Certificate.VerifyNoRevocation()` för att fungera.

> [!TIP]
> Problem med deadlock för klient certifikat som beskrivs i den här [artikeln](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) kan visas på flera olika sätt, t. ex. `403 Forbidden` begär Anden som låser sig, `context.Request.Certificate` resulterar `null`i status kod efter att tids gränsen är klar. Det här problemet påverkar `POST` vanligt `PUT` vis och begär Anden med en innehålls längd på ungefär 60KB eller större.
> För att förhindra att det här problemet inträffar aktiverar du inställningen "förhandla klient certifikat" för önskade värdnamn på bladet "anpassade domäner" som visas nedan. Den här funktionen är inte tillgänglig i förbruknings nivån.

![Förhandla klientcertifikat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Nästa steg

-   [Skydda backend-tjänster med autentisering av klient certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Ladda upp certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
