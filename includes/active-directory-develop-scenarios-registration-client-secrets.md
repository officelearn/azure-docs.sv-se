---
title: ta med fil
description: inkludera fil för klient scenariots landnings sidor (daemon, webbapp, webb-API)
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
ms.openlocfilehash: 8f98808aa0f8a2c32e2117447824114747091a82
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912504"
---
## <a name="registration-of-secrets-or-certificates"></a>Registrering av hemligheter eller certifikat

Som ett konfidentiellt klient program måste du registrera ett hemligt eller certifikat. Du kan registrera dina program hemligheter antingen via den interaktiva miljön i [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)eller använda kommando rads verktyg (som PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrera klient hemligheter med hjälp av program registrerings portalen

Hantering av klientautentiseringsuppgifter sker på sidan **certifikat & hemligheter** för ett program:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- program hemligheten (kallas även klient hemlighet) genereras av Azure AD under registreringen av det konfidentiella klient programmet. Den här generationen sker när du väljer **ny klient hemlighet**. Vid det här skedet måste du kopiera den hemliga strängen i Urklipp för användning i appen innan du väljer **Spara**. Den här strängen visas inte längre.
- certifikatet laddas upp i program registreringen med hjälp av knappen **Ladda upp certifikat** . Azure AD stöder bara certifikat som är direkt registrerade i programmet och inte följer certifikat kedjor.

Mer information finns i [snabb start: Konfigurera ett klient program för att få åtkomst till webb-API: er | Lägg till autentiseringsuppgifter i programmet](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Registrera klient hemligheter med PowerShell

Alternativt kan du registrera ditt program med Azure AD med hjälp av kommando rads verktyg. Exemplet [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) visar hur du registrerar en program hemlighet eller ett certifikat med ett Azure AD-program:

- Mer information om hur du registrerar en program hemlighet finns i [AppCreationScripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Mer information om hur du registrerar ett certifikat med programmet finns i [AppCreationScripts-withCert/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
