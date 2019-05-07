---
title: ta med fil
description: Inkludera fil för konfidentiell klient scenariot landningssidor (daemon, webbapp, webb-API)
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
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074657"
---
## <a name="registration-of-secrets-or-certificates"></a>Registrering av hemligheter eller certifikat

T.ex. för klientprogram konfidentiell behöver du registrera en hemlighet eller certifikat. Du kan registrera dina programhemligheter via den interaktiva miljön i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), eller med hjälp av kommandoradsverktyg (t.ex. PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrera klienten hemligheter med hjälp av portalen för registrering av program

Hantering av klientens autentiseringsuppgifter sker i den **certifikat och hemligheter** för ett program:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- programhemlighet (även namngivna klienthemlighet) skapas av Azure AD vid registrering av konfidentiell klientprogrammet. Den här generation händer när du väljer **nya klienthemligheten**. I det här läget måste du kopiera den hemliga strängen i Urklipp för användning i din app innan du väljer **spara**. Den här strängen kommer inte längre att visas.
- certifikatet har laddats upp i registreringen programmet med den **överför certifikat** knappen

Mer information finns i [snabbstarten: Konfigurera ett klientprogram för att få åtkomst till webb-API: er | Lägg till autentiseringsuppgifter i ditt program](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Registrera klienten hemligheter med hjälp av PowerShell

Du kan även registrera ditt program med Azure AD med hjälp av kommandoradsverktyg. Den [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) exemplet visar hur du registrerar en programhemlighet eller ett certifikat med ett Azure AD-program:

- Mer information om hur du registrerar en programhemlighet finns i [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Mer information om hur du registrerar ett certifikat med programmet finns i [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)