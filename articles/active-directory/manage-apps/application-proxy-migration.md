---
title: Uppgradera till Azure AD-programproxy | Microsoft-dokument
description: Välj vilken proxylösning som är bäst om du uppgraderar från Microsoft Forefront eller Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108411"
---
# <a name="compare-remote-access-solutions"></a>Jämför fjärråtkomstlösningar

Azure Active Directory Application Proxy är en av två fjärråtkomstlösningar som Microsoft erbjuder. Den andra är Web Application Proxy, den lokala versionen. Dessa två lösningar ersätter tidigare produkter som Microsoft har erbjudit: Microsoft Forefront Threat Management Gateway (TMG) och Unified Access Gateway (UAG). Använd den här artikeln för att förstå hur dessa fyra lösningar står sig mot varandra. För er som fortfarande använder de inaktuella TMG- eller UAG-lösningarna använder du den här artikeln för att planera migreringen till en av programproxyn. 


## <a name="feature-comparison"></a>Jämför funktioner

Använd den här tabellen för att förstå hur Threat Management Gateway (TMG), Unified Access Gateway (UAG), WAP (Web Application Proxy) och Azure AD Application Proxy (AP) står sig mot varandra.

| Funktion | Tmg | UAG (olika) | WAP | AP |
| ------- | --- | --- | --- | --- |
| Certifikatsautentisering | Ja | Ja | - | - |
| Publicera webbläsarappar selektivt | Ja | Ja | Ja | Ja |
| Preauthentication och enkel inloggning | Ja | Ja | Ja | Ja | 
| Layer 2/3 brandvägg | Ja | Ja | - | - |
| Vidarebefordra proxyfunktioner | Ja | - | - | - |
| VPN-funktioner | Ja | Ja | - | - |
| Stöd för omfattande protokoll | - | Ja | Ja, om du kör över HTTP | Ja, om du kör över HTTP eller via Fjärrskrivbordsgateway |
| Fungerar som ADFS-proxyserver | - | Ja | Ja | - |
| En portal för programåtkomst | - | Ja | - | Ja |
| Översättning av svarstextlänk | Ja | Ja | - | Ja | 
| Autentisering med rubriker | - | Ja | - | Ja, med PingAccess | 
| Säkerhet i molnskala | - | - | - | Ja | 
| Villkorlig åtkomst | - | Ja | - | Ja |
| Inga komponenter i den demilitariserade zonen (DMZ) | - | - | - | Ja |
| Inga inkommande anslutningar | - | - | - | Ja |

I de flesta scenarier rekommenderar vi Azure AD Application Proxy som den moderna lösningen. Proxy för webbprogram är bara att föredra i scenarier som kräver en proxyserver för AD FS och du kan inte använda anpassade domäner i Azure Active Directory. 

Azure AD Application Proxy erbjuder unika fördelar jämfört med liknande produkter, inklusive:

- Utöka Azure AD till lokala resurser
   - Säkerhet och skydd i molnskala
   - Funktioner som villkorlig åtkomst och multifaktorautentisering är enkla att aktivera
- Inga komponenter i den demilitariserade zonen
- Inga inkommande anslutningar krävs
- En åtkomstpanel som användarna kan gå till för alla sina program, inklusive O365, Azure AD-integrerade SaaS-appar och dina lokala webbappar. 


## <a name="next-steps"></a>Nästa steg

- [Använd Azure AD-program för att ge säker fjärråtkomst till lokala program](application-proxy.md)
