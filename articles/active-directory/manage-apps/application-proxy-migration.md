---
title: Uppgradera till Azure AD Application Proxy | Microsoft Docs
description: Välj vilken proxy-lösning som är bäst om du uppgraderar från Microsoft Forefront eller enhetlig åtkomst Gateway.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 313827b1061b2d0e37db0396581079516681e257
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960725"
---
# <a name="compare-remote-access-solutions"></a>Jämför fjärråtkomstlösningar

Azure Active Directory Application Proxy är en av två fjärråtkomstlösningarna som Microsoft erbjuder. Den andra är Web Application Proxy, lokal version. Dessa två lösningar ersätter tidigare produkter som erbjuds av Microsoft: Microsoft Forefront Threat Management Gateway (TMG) och enhetlig åtkomst Gateway (UAG). Använd den här artikeln för att förstå hur dessa fyra lösningar jämfört med varandra. Använd den här artikeln för att planera din migrering till en av programproxyn för de som fortfarande använder de inaktuella TMG eller UAG lösningarna. 


## <a name="feature-comparison"></a>Jämför funktioner

Använd den här tabellen för att förstå hur Threat Management Gateway (TMG), en enhetlig åtkomst Gateway (UAG), Web Application Proxy (WAP) och Azure AD Application Proxy (AP) jämfört med varandra.

| Funktion | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Certifikatautentisering | Ja | Ja | - | - |
| Selektivt publicera webbläsarbaserade appar | Ja | Ja | Ja | Ja |
| Förautentisering och enkel inloggning | Ja | Ja | Ja | Ja | 
| Layer 2/3 brandvägg | Ja | Ja | - | - |
| Vidarebefordra proxyfunktioner | Ja | - | - | - |
| VPN-funktioner | Ja | Ja | - | - |
| Omfattande protokollstöd | - | Ja | Ja, om du kör över HTTP | Ja, om du kör via HTTP eller via Remote Desktop Gateway |
| Fungerar som AD FS-proxyservern | - | Ja | Ja | - |
| En portal för programåtkomst | - | Ja | - | Ja |
| Svaret brödtext länköversättning | Ja | Ja | - | Ja | 
| Autentisering med rubriker | - | Ja | - | Ja, med PingAccess | 
| Säkerhet för molnskala | - | - | - | Ja | 
| Villkorlig åtkomst | - | Ja | - | Ja |
| Inga komponenter i demilitariserad zon (DMZ) | - | - | - | Ja |
| Inga inkommande anslutningar | - | - | - | Ja |

För de flesta fall rekommenderar vi Azure AD-program till den moderna lösningen. Du kan inte använda anpassade domäner i Azure Active Directory Web Application Proxy är bara önskade i scenarier som kräver en proxyserver för AD FS. 

Azure AD-programproxyn erbjuder unika fördelar jämfört med liknande produkter, inklusive:

- Utöka Azure AD till lokala resurser
   - Skalbar säkerhet och skydd
   - Funktioner som villkorlig åtkomst och Multi-Factor Authentication är enkelt att aktivera
- Inga komponenter i demilitariserad zon
- Inga inkommande anslutningar som krävs
- En åtkomstpanelen att användarna kan gå till för alla program, inklusive O365, Azure AD-integrerade SaaS-appar och din lokala webbappar. 


## <a name="next-steps"></a>Nästa steg

- [Använda Azure AD-program för att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md)
- [Övergången från Forefront TMG och UAG tillämpningsproxy](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
