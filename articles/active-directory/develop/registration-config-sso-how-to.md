---
title: Konfigurera enkel inloggning för program
description: Så här konfigurerar du enkel inloggning för ett anpassat program som du utvecklar och registrerar med Azure AD.
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
ms.openlocfilehash: 9b6451d29a3a874e09d74cbe664662a395b647f9
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890468"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Så här konfigurerar du enkel inloggning för ett program

Aktivering av federerad enkel inloggning (SSO) i appen aktive ras automatiskt när du federerar genom att använda Azure AD för OpenID Connect, SAML 2,0 eller WS-utfodras. Om slutanvändarna behöver logga in trots att de redan har en befintlig session med Azure AD, är det troligt att appen är felkonfigurerad.

* Om du använder ADAL/MSAL ser du till att du har **PromptBehavior** inställd på **Auto** i stället för **Always**.

* Om du skapar en mobilapp kan du behöva ytterligare konfigurationer för att aktivera Brokered eller non-Brokered SSO.

För Android, se [aktivera enkel inloggning mellan appar i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

För iOS, se [Aktivera Cross app SSO i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Nästa steg

[Enkel inloggning i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Aktivera enkel inloggning mellan appar i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Aktivera enkel inloggning mellan appar i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrera appar till AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Behörigheter och medgivande i Microsoft Identity Platform-slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
