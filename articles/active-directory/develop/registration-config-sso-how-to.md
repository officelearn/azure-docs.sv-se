---
title: Konfigurera enkel inloggning för program | Microsoft Docs
description: Så här konfigurerar du enkel inloggning för ett anpassat program som du utvecklar och registrerar med Azure AD.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc9f20903d65fbde161d26ed3e194e63d341ccb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320962"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Så här konfigurerar du enkel inloggning för ett program

Aktivering av federerad enkel inloggning (SSO) i appen aktive ras automatiskt när du federerar genom att använda Azure AD för OpenID Connect, SAML 2,0 eller WS-utfodras. Om slutanvändarna behöver logga in trots att de redan har en befintlig session med Azure AD, är det troligt att appen är felkonfigurerad.

* Om du använder ADAL/MSAL ser du till att du har **PromptBehavior** inställd på **Auto** i stället för Always.

* Om du skapar en mobilapp kan du behöva ytterligare konfigurationer för att aktivera Brokered eller non-Brokered SSO.

För Android, se [aktivera enkel inloggning mellan appar i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

För iOS, se [Aktivera Cross app SSO i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Nästa steg

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Aktivera enkel inloggning mellan appar i Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Aktivera enkel inloggning mellan appar i iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrera appar till AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Medgivande och behörighet för AzureAD v 2.0 konvergerade appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
