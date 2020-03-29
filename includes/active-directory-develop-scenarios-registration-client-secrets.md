---
title: ta med fil
description: inkludera fil för konfidentiella målsidor för klientscenario (demon, webbapp, webb-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773395"
---
## <a name="register-secrets-or-certificates"></a>Registrera hemligheter eller certifikat

När det gäller alla konfidentiella klientprogram måste du registrera en hemlighet eller ett certifikat. Du kan registrera dina programhemligheter antingen via den interaktiva upplevelsen i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) eller genom att använda kommandoradsverktyg (som PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrera klienthemligheter med hjälp av programregistreringsportalen

Hanteringen av klientautentiseringsuppgifter sker på sidan **Certifikat & hemligheter** för ett program:

![Sidan Certifikat & hemligheter](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Programhemligheten (även namnet klienthemligheten) genereras av Azure AD under registreringen av det konfidentiella klientprogrammet. Den här generationen inträffar när du väljer **Ny klienthemlighet**. Då måste du kopiera den hemliga strängen till Urklipp för användning i appen innan du väljer **Spara**. Den här strängen visas inte längre.
- Under programregistreringen använder du knappen **Ladda upp certifikat** för att ladda upp certifikatet. Azure AD stöder endast certifikat som är direkt registrerade i programmet och följer inte certifikatkedjor.

Mer information finns i [Snabbstart: Konfigurera ett klientprogram för åtkomst till webb-API:er | Lägg till autentiseringsuppgifter i programmet](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Registrera klienthemligheter med PowerShell

Du kan också registrera ditt program med Azure AD med hjälp av kommandoradsverktyg. Exemplet [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) visar hur du registrerar ett programhemlighet eller certifikat med ett Azure AD-program:

- Mer information om hur du registrerar en programhemlighet finns i [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Mer information om hur du registrerar ett certifikat med ett program finns i [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
