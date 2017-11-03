---
title: "Översikt över princip SSL för Azure Programgateway | Microsoft Docs"
description: "Lär dig mer om hur Azure Application Gateway kan du konfigurera SSL-policy"
services: application gateway
documentationcenter: na
author: amsriva
manager: 
editor: 
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: ec36af282bbfdc22ff88082412dd18cd2a85f245
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-ssl-policy-overview"></a>Översikt över princip Gateway SSL

Du kan använda Azure Programgateway centralisera SSL-certifikathantering och minska omkostnader för kryptering och dekryptering från en backend-servergrupp. Den här centraliserade SSL även hanterar kan du ange en princip för central SSL som är anpassade till din organisations säkerhetskrav. Detta hjälper dig att uppfylla kompatibilitetskraven samt riktlinjer och rekommenderade metoder.

SSL-policy inkluderar kontroll av versionen av SSL-protokollet samt krypteringssviterna och i vilken ordning där chiffer används under en SSL-handskakning. Programgateway erbjuder två metoder för att styra SSL-policy. Du kan använda en fördefinierad princip eller en anpassad princip.

## <a name="predefined-ssl-policy"></a>Fördefinierade SSL-policy

Application Gateway har tre fördefinierade säkerhetsprinciper. Du kan konfigurera din gateway med någon av dessa principer för att hämta lämplig säkerhetsnivå. Namn för principen har kommenterats per år och månad som de har konfigurerats. Varje princip erbjuder olika SSL-protokollversioner och krypteringssviter. Vi rekommenderar att du använder de senaste principerna som SSL för att säkerställa bästa SSL-säkerhet.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Standard| True (om ingen fördefinierad princip har angetts) |
|Krypteringssviter     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Egenskap  |Värde  |
|   ---      |  ---       |
|Namn     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Standard| False |
|Krypteringssviter     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Standard| False |
|Krypteringssviter     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Anpassade SSL-policy

Om en fördefinierad princip SSL måste konfigureras för dina behov, måste du definiera dina egna anpassade SSL-policy. Med en anpassad princip för SSL har du fullständig kontroll över SSL-protokollet minimiversionen att stödja samt krypteringssviter som stöds och deras prioritetsordning.
 
### <a name="ssl-protocol-versions"></a>SSL-protokollversioner

* SSL 2.0 och 3.0 är inaktiverade som standard för alla programgatewayer. Dessa protokollversioner kan inte konfigureras.
* En anpassad princip för SSL ger dig möjlighet att välja något av följande tre protokoll som SSL-protokollet minimiversionen för din gateway: TLSv1_0, TLSv1_1 och TLSv1_2.
* Om inga SSL-policy har definierats, aktiveras alla tre protokoll (TLSv1_0 TLSv1_1 och TLSv1_2).

### <a name="cipher-suites"></a>Krypteringssviter

Application Gateway har stöd för de följande krypteringssviter som du kan välja den anpassade principen. Sorteringen av krypteringssviterna som anger prioritetsordningen under SSL-förhandling.


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

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att konfigurera ett SSL-policy, se [Konfigurera SSL-policy på en Programgateway](application-gateway-configure-ssl-policy-powershell.md).
