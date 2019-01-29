---
title: Så här konfigurerar du ett nytt program för flera klienter | Microsoft Docs
description: Hur du konfigurerar enkel inloggning för ett anpassat program som du utvecklar och registrera med Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: be85456c94fbc156827157705bc29169bbae9cdd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096774"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Så här konfigurerar du ett nytt program för flera innehavare

Aktivera federerad enkel inloggning (SSO) i din app aktiveras automatiskt när federering via Azure AD för OpenID Connect, SAML 2.0 eller WS-Fed. Om slutanvändarna har problem att logga in trots redan har en befintlig session med Azure AD, är det troligt att din app kan vara felkonfigurerad.

* Om du använder ADAL/MSAL, kontrollera att du har **PromptBehavior** inställd **automatisk** snarare än **alltid**.

* Om du bygger en mobil app kan behöva ytterligare konfigurationer kan aktivera SSO för asynkrona eller icke asynkron.

För Android, se [aktiverar mellan App enkel inloggning i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

IOS-, se [aktiverar mellan enkel inloggning appar i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Nästa steg

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Aktivera enkel inloggning för appar i Android för olika plattformar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Aktivera Cross enkel inloggning appar i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrera appar till AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Medgivande och ge behörighet till för AzureAD v2.0 konvergerat appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
