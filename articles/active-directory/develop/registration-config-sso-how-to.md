---
title: Konfigurera enkel inloggning för program
description: Konfigurera enkel inloggning för ett anpassat program som du utvecklar och registrerar med Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 630be1676d73410f1b0d376b163a8599299ddbe2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883159"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Konfigurera enkel inloggning för ett program

Aktivera federerade enkel inloggning (SSO) i din app aktiveras automatiskt när du matar via Azure AD för OpenID Connect, SAML 2.0 eller WS-Fed. Om dina slutanvändare måste logga in trots att de redan har en befintlig session med Azure AD, är det troligt att din app kan vara felkonfigurerad.

* Om du använder ADAL/MSAL kontrollerar du att **PromptBehavior** är inställt på **Auto** i stället **för Alltid**.

* Om du skapar en mobilapp kan du behöva ytterligare konfigurationer för att aktivera förmedlade eller icke-förmedlade SSO.

För Android finns i [Aktivera Cross App SSO i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

För iOS finns i [Aktivera Cross App SSO i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Nästa steg

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Aktivera SSO för korsappar i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Aktivera SSO för korsapp i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrera appar i AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Medgivande och behörighet för AzureAD v2.0-konvergerade appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
