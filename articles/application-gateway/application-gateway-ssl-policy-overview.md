---
title: Översikt över SSL-princip för Azure Application Gateway
description: Lär dig hur du konfigurerar SSL-princip för Azure Application Gateway och minskar belastningen på kryptering och dekryptering från en Server grupp på Server sidan.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: fe70bd5994d835bdc2651a64d35c988ea38b8511
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770041"
---
# <a name="application-gateway-ssl-policy-overview"></a>Översikt över Application Gateway SSL-policy

Du kan använda Azure Application Gateway för att centralisera SSL-certifikat hantering och minska belastningen på kryptering och dekryptering från en Server grupp på Server sidan. Med den här centraliserade SSL-hanteringen kan du också ange en central SSL-princip som passar organisationens säkerhets krav. Detta hjälper dig att uppfylla kraven för efterlevnad samt säkerhets rikt linjer och rekommenderade metoder.

SSL-principen inkluderar kontroll av SSL-protokollets version samt chiffersviter och i vilken ordning chiffer används under en SSL-handskakning. Application Gateway erbjuder två mekanismer för att kontrol lera SSL-principen. Du kan använda antingen en fördefinierad princip eller en anpassad princip.

## <a name="predefined-ssl-policy"></a>Fördefinierad SSL-princip

Application Gateway har tre fördefinierade säkerhets principer. Du kan konfigurera din gateway med någon av dessa principer för att få rätt säkerhets nivå. Princip namnen är kommenterade efter det år och den månad då de konfigurerades. Varje princip erbjuder olika versioner av SSL-protokoll och chiffersviter. Vi rekommenderar att du använder de nyaste SSL-principerna för att säkerställa bästa SSL-säkerhet.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| Sant (om ingen fördefinierad princip har angetts) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Egenskap  |Värde  |
|   ---      |  ---       |
|Namn     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Default| Falskt |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Default| Falskt |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Anpassad SSL-princip

Om en fördefinierad SSL-princip måste konfigureras för dina krav måste du definiera en egen anpassad SSL-princip. Med en anpassad SSL-princip har du fullständig kontroll över den lägsta versionen av SSL-protokollet som stöder, samt de chiffersviter som stöds och deras prioritetsordning.
 
### <a name="ssl-protocol-versions"></a>SSL-protokoll versioner

* SSL 2,0 och 3,0 är inaktiverat som standard för alla programgatewayer. Dessa protokoll versioner kan inte konfigureras.
* En anpassad SSL-princip ger dig möjlighet att välja något av följande tre protokoll som lägsta SSL-protokoll version för din Gateway: TLSv1_0, TLSv1_1 och TLSv1_2.
* Om ingen SSL-princip har definierats är alla tre protokollen (TLSv1_0, TLSv1_1 och TLSv1_2) aktiverade.

### <a name="cipher-suites"></a>Chiffersviter

Application Gateway stöder följande chiffersviter som du kan använda för att välja en anpassad princip. Ordningen för chiffersviter avgör prioritetsordningen under SSL-förhandlingen.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> SSL-chiffersviter som används för anslutningen baseras också på typen av certifikat som används. I klient till Application Gateway-anslutningar baseras de chiffersviter som används på typen av Server certifikat i Application Gateway-lyssnaren. De chiffersviter som används i Application Gateway till backend-anslutningspoolen baseras på typen av Server certifikat på backend-poolens servrar.

## <a name="known-issue"></a>Kända problem
Application Gateway v2 stöder för närvarande inte följande chiffer:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du konfigurerar en SSL-princip läser du [Konfigurera SSL-princip på en Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
