---
title: SAML-autentisering med Azure Active Directory
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
ms.openlocfilehash: 05b13e04db8e83a8a10c2d7fe0aea202dfa3b69c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114506"
---
# <a name="saml-authentication-with-azure-active-directory"></a>SAML-autentisering med Azure Active Directory

Security Assertion Markup Language (SAML) är en öppen standard för att utbyta autentiserings-och auktoriseringsdata mellan en identitets leverantör och en tjänst leverantör. SAML är ett XML-baserat kodspråk för säkerhets kontroller, som är instruktioner som tjänst leverantörer använder för att fatta beslut om åtkomst kontroll. 

SAML-specifikationen definierar tre roller:

* Huvudobjektet, vanligt vis en användare
* Identitets leverantören (IdP)
* Tjänste leverantören (SP)


## <a name="use-when"></a>Använd när

Det finns ett behov av att tillhandahålla enkel inloggning (SSO) för ett Enterprise SAML-program.

En av de viktigaste användnings fallen är att SAML-adresser är SSO, särskilt genom att utöka SSO över säkerhets domäner, men det finns även andra användnings fall (kallas profiler). 

![arkitektur diagram](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: begär en tjänst från programmet.

* **Webbläsare**: komponenten som användaren interagerar med.

* **Webbapp**: företags program som stöder SAML och använder Azure AD som IDP.

* **Token**: en SAML-kontroll (kallas även SAML-token) som utför uppsättningar av anspråk gjorda av IDP om principen (användaren). Den innehåller information om autentisering, attribut och auktorisering.

* **Azure AD**: Enterprise Cloud-IDP som ger enkel inloggning och Multi-Factor Authentication för SAML-appar. Den synkroniserar, underhåller och hanterar identitets information för användare samtidigt som de tillhandahåller autentiserings tjänster för att förlita sig på program. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementera SAML-autentisering med Azure AD

* [Självstudier för att integrera SaaS-program med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Konfigurera SAML-baserad enkel inloggning för program som inte är gallerier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Så här använder Azure AD SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
