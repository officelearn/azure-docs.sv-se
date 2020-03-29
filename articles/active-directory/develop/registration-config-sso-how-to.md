---
title: Konfigurera enkel inloggning för program | Microsoft-dokument
description: Konfigurera enkel inloggning för ett anpassat program som du utvecklar och registrerar med Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702649"
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
