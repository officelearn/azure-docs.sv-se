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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87103034"
---
## <a name="register-secrets-or-certificates"></a>Registrera hemligheter eller certifikat

Som för alla konfidentiella klient program måste du registrera ett hemligt eller certifikat. Du kan registrera dina program hemligheter antingen via den interaktiva miljön i [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) eller genom att använda kommando rads verktyg (som PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrera klient hemligheter med hjälp av program registrerings portalen

Hantering av klientautentiseringsuppgifter sker på sidan **certifikat & hemligheter** för ett program:

![Sidan certifikat & hemligheter](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Du skapar en *klient hemlighet* genom att välja **ny klient hemlighet** i appens registrering i Azure Portal. När du skapar en klient hemlighet _måste_ du registrera hemlighetens sträng innan du navigerar bort från fönstret **certifikat & hemligheter** . Hemlighetens sträng visas aldrig igen.
- Under program registrering använder du knappen **Ladda upp certifikat** för att ladda upp certifikatet. Azure AD stöder bara certifikat som är direkt registrerade i programmet och som inte följer certifikat kedjor.

Mer information finns i [snabb start: Konfigurera ett klient program för åtkomst till webb-API: er | Lägg till autentiseringsuppgifter i ditt program](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Registrera klient hemligheter med hjälp av PowerShell

Alternativt kan du registrera ditt program med Azure AD med hjälp av kommando rads verktyg. Exemplet [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) visar hur du registrerar en program hemlighet eller ett certifikat med ett Azure AD-program:

- Mer information om hur du registrerar en program hemlighet finns i [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Mer information om hur du registrerar ett certifikat med ett program finns i [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
