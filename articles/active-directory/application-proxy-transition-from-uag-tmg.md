---
title: Uppgradera till Azure AD Application Proxy | Microsoft Docs
description: "Välj vilka proxy-lösning som är bäst om du uppgraderar från Microsoft Forefront eller enhetlig åtkomst Gateway."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5323a9225adb9a9bfca2dccec5c84f288c4fd25d
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="compare-remote-access-solutions"></a>Jämför fjärråtkomstlösningar

Azure Active Directory Application Proxy är en av två fjärråtkomstlösningarna som Microsoft erbjuder. Den andra är Web Application Proxy, lokal version. Dessa två lösningar ersätta tidigare produkter som erbjuds av Microsoft: Microsoft Forefront Threat Management Gateway (TMG) och enhetlig åtkomst Gateway (UAG). Använd den här artikeln för att förstå hur dessa fyra lösningar förhåller sig till varandra. För de som du fortfarande använder använder de föråldrade TMG eller UAG-lösningarna den här artikeln för att planera migreringen till en Application Proxy. 


## <a name="feature-comparison"></a>Jämför funktioner

Använd den här tabellen för att förstå hur Threat Management Gateway (TMG), enhetlig åtkomst Gateway (UAG), Webbprogramproxy (WAP) och Azure AD Application Proxy (AP) förhåller sig till varandra.

| Funktion | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Certifikatautentisering | Ja | Ja | - | - |
| Selektivt publicera webbläsarbaserade appar | Ja | Ja | Ja | Ja |
| Förautentisering och enkel inloggning | Ja | Ja | Ja | Ja | 
| Nivå 2/3 brandväggen | Ja | Ja | - | - |
| Vidarebefordra proxyfunktioner | Ja | - | - | - |
| VPN-funktioner | Ja | Ja | - | - |
| Omfattande protokollstöd | - | Ja | Ja, om du kör via HTTP | Ja, om du kör via HTTP eller via fjärrskrivbordsgateway |
| Fungerar som AD FS-proxyservern | - | Ja | Ja | - |
| En portal för programåtkomst | - | Ja | - | Ja |
| Svaret brödtext länköversättning | Ja | Ja | - | Ja | 
| Autentisering med rubriker | - | Ja | - | Ja, med PingAccess | 
| Skalbar molnlagring säkerhet | - | - | - | Ja | 
| Villkorlig åtkomst | - | Ja | - | Ja |
| Inga komponenter i demilitariserad zon (DMZ) | - | - | - | Ja |
| Inga inkommande anslutningar | - | - | - | Ja |

För de flesta fall rekommenderar vi Azure AD-program som moderna lösning. Du kan inte använda anpassade domäner i Azure Active Directory Web Application Proxy är bara önskade i scenarier som kräver en proxyserver för AD FS. 

Azure AD Application Proxy har unika fördelar jämfört med liknande produkter, inklusive:

- Utöka Azure AD till lokala resurser
   - Skalbar molnlagring säkerhet och skydd
   - Funktioner som villkorlig åtkomst och Multi-Factor Authentication är lätta att aktivera
- Ingen Component i demilitariserad zon
- Inga inkommande anslutningar som krävs
- En åtkomstpanelen att användarna kan gå till för alla sina program, inklusive O365, Azure AD-integrerade SaaS-appar och dina lokala web apps. 


## <a name="next-steps"></a>Nästa steg

- [Använda Azure AD-program för att ge säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md)
- [Övergången från Forefront TMG och UAG till Application Proxy](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
