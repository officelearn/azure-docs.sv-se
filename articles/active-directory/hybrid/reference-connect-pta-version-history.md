---
title: 'Direkt autentisering i Azure AD: versions historik | Microsoft Docs'
description: Den här artikeln innehåller alla versioner av Azure AD-direktautentisering
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786460"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD-direktautentisering: versions historik 
 
Agenterna som är installerade lokalt och som möjliggör direktautentisering uppdateras regelbundet för att tillhandahålla nya funktioner. Den här artikeln innehåller de versioner och funktioner som läggs till när nya funktioner lanseras. Direkt autentiserings agenter uppdateras automatiskt när en ny version släpps. 

Här är relaterade ämnen: 

- [Användar inloggning med Azure AD-direktautentisering](how-to-connect-pta.md) 
- [Installation av Azure AD-direktautentisering](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Versions status 
1/22/2019: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd har lagts till för Service Bus pålitliga kanaler för att lägga till ett annat lager av anslutnings återhämtning för utgående anslutningar 
- Genomdriva TLS 1,2 under agent registreringen 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Versions status 
4/10/2018: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd för WebSocket-anslutning 
- Ange TLS 1,2 som standard protokoll för agenten 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Versions status 
1/31/2018: har släppts för nedladdning  
### <a name="fixed-issues"></a>Åtgärdade problem 

- En bugg har åtgärd ATS som orsakade minnes läckor i agenten. 
- Den Azure Service Bus versionen har uppdaterats, vilket innehåller en fel korrigering för anslutnings-timeout-problem. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Versions status 
11/26/2017: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd har lagts till för WebSocket-baserade anslutningar mellan agenten och Azure AD-tjänster för att förbättra anslutnings återhämtning 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Versions status 
11/25/2017: har släppts för nedladdning  
### <a name="fixed-issues"></a>Åtgärdade problem 
- Fasta buggar som är relaterade till DNS-cachen för standard scenarier för proxy 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Versions status 
10/17/2017: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Funktioner för DNS-cache har lagts till för utgående anslutningar för att lägga till återhämtning från DNS-problem 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Versions status 
08/31/2017: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- GA-version av Azure AD-direktautentisering 

## <a name="next-steps"></a>Nästa steg

- [Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)