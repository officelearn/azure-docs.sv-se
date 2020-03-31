---
title: 'Azure AD-direktautentisering: versionsversionshistorik | Microsoft-dokument'
description: I den här artikeln visas alla versioner av Azure AD-direktautentiseringsagenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786460"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD-direktautentiseringsagent: Versionsversionshistorik 
 
De agenter som installeras lokalt och som möjliggör direktautentisering uppdateras regelbundet för att tillhandahålla nya funktioner. I den här artikeln visas de versioner och funktioner som läggs till när nya funktioner introduceras. Direktautentiseringsagenter uppdateras automatiskt när en ny version släpps. 

Här är relaterade ämnen: 

- [Användarloggning med Azure AD-direktautentisering](how-to-connect-pta.md) 
- [Azure AD-installation av autentiseringsagent](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Versionsstatus 
2019-01-22: Släppt för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Lagt till stöd för Service Bus tillförlitliga kanaler för att lägga till ytterligare ett lager av anslutning återhämtning för utgående anslutningar 
- Framtvinga TLS 1.2 under agentregistrering 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Versionsstatus 
2018-04-10: Släppt för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd för webbuttagsanslutning 
- Ange TLS 1.2 som standardprotokoll för agenten 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Versionsstatus 
2018-01-31: Släppt för nedladdning  
### <a name="fixed-issues"></a>Åtgärdade problem 

- Fixade en bugg som orsakade några minnesläckor i agenten. 
- Uppdaterade Azure Service Bus-versionen, som innehåller en felkorrigering för tidsgränsen för anslutning. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Versionsstatus 
2017-11-26: Släppt för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Lade till stöd för websocketbaserade anslutningar mellan agent- och Azure AD-tjänster för att förbättra anslutningens återhämtningsförmåga 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Versionsstatus 
2017-11-25: Släppt för nedladdning  
### <a name="fixed-issues"></a>Åtgärdade problem 
- Fasta fel relaterade till DNS-cachen för standardproxyscenarier 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Versionsstatus 
2017-10-17: Släppt för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Lade till DNS-cachefunktioner för utgående anslutningar för att lägga till återhämtning från DNS-fel 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Versionsstatus 
2017-08-31: Släppt för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- GA-versionen av Azure AD-direktautentiseringsagenten 

## <a name="next-steps"></a>Nästa steg

- [Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)