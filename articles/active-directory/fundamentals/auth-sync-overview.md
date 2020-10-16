---
title: Översikt över Azure Active Directory Authentication and Synchronization Protocol
description: Arkitektur vägledning för att uppnå detta autentiserings mönster
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114497"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Azure Active Directory integreringar med äldre autentiserings-och synkroniseringsfilter

Microsoft Azure Active Directory (Azure AD) möjliggör integrering med många autentiserings-och protokoll för synkronisering. Med hjälp av integreringen av autentisering kan du använda Azure AD och dess säkerhets-och hanterings funktioner med små eller inga ändringar i dina program som använder äldre autentiseringsmetoder. Med synkroniseringen av synkronisering kan du synkronisera användar-och grupp data till Azure AD och sedan användar hanterings funktionerna för Azure AD. Vissa Sync-mönster aktiverar även automatisk etablering.

## <a name="authentication-patterns"></a>Autentiseringsmönster

I följande tabell visas verifierings mönster och deras funktioner. Välj namnet på ett mönster för autentisering som ska visas

* En detaljerad beskrivning

* När du ska använda detta

* Arkitektur diagram

* Förklaring av system komponenter

* Länkar till hur du implementerar integreringen

 

| Autentiseringsmönster| Autentisering| Auktorisering| Multifaktorautentisering| Villkorlig åtkomst |
| - |- | - | - | - |
| [Huvud-baserad autentisering](auth-header-based.md)|![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [LDAP-autentisering](auth-ldap.md)| ![Bock markering](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2,0-autentisering](auth-oauth2.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [OIDC-autentisering](auth-oidc.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Lösenordsbaserad SSO-autentisering](auth-password-based-sso.md )| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [RADIUS-autentisering]( auth-radius.md)| ![Bock markering](./media/authentication-patterns/check.png)| | ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Tjänster för fjärrskrivbordsgateway](auth-remote-desktop-gateway.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [SAML-autentisering](auth-saml.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Windows-autentisering – Kerberos-begränsad delegering](auth-kcd.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Mönster för synkronisering

I följande tabell visas synkroniseringsresultat och deras funktioner. Välj namnet på ett mönster som ska visas

* En detaljerad beskrivning

* När du ska använda detta

* Arkitektur diagram

* Förklaring av system komponenter

* Länkar till hur du implementerar integreringen



| Mönster för synkronisering| Katalogsynkronisering| Användaretablering |
| - | - | - |
| [Katalogsynkronisering](sync-directory.md)| ![Bock markering](./media/authentication-patterns/check.png)|  |
| [LDAP-synkronisering](sync-ldap.md)| ![Bock markering](./media/authentication-patterns/check.png)|  |
| [SCIM-synkronisering](sync-scim.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |

