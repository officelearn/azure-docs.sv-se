---
title: Använda Azure AD för program som inte finns med i app-galleriet
description: Förstå hur du integrerar appar som inte finns med i Azure AD-galleriet.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3483701d34b8fef5b40a411e22b33a73db2de44b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658748"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Använda Azure AD för program som inte finns med i app-galleriet

I snabb starten för att [lägga till en app](add-application-portal.md) , lär du dig hur du lägger till en app i Azure AD-klienten.

Förutom valen i [Azure AD-programgalleriet](../saas-apps/tutorial-list.md)har du möjlighet att lägga till ett **program som inte är ett galleri program**. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Funktioner för appar som inte visas i Azure AD-galleriet

Du kan lägga till alla program som redan finns i din organisation eller program från tredje part från en leverantör som inte redan ingår i Azure AD-galleriet. Beroende på ditt [licens avtal](https://azure.microsoft.com/pricing/details/active-directory/)är följande funktioner tillgängliga:

- Självbetjänings integrering av alla program som stöder [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) identitets leverantörer (SP-initierad eller IDP-initierad)
- Självbetjänings integrering av alla webb program som har en HTML-baserad inloggnings sida med [LÖSENORDSBASERAD SSO](sso-options.md#password-based-sso)
- Anslutning via självbetjäning för program som använder [systemet för scim-protokoll (Cross-Domain Identity Management) för användar etablering](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Möjlighet att lägga till länkar till ett program i [Office 365-appen starta](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Mina appar](sso-options.md#linked-sign-on)

Om du vill ha råd om utvecklare om hur du integrerar anpassade appar med Azure AD, se [autentiserings scenarier för Azure AD](../develop/authentication-vs-authorization.md). När du utvecklar en app som använder ett modernt protokoll som [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) för att autentisera användare, kan du registrera den med Microsoft Identity Platform genom att använda [Appregistreringar](../develop/quickstart-register-app.md) upplevelse i Azure Portal.

## <a name="next-steps"></a>Nästa steg

- [Snabb starts serie på App Management](view-applications-portal.md)