---
title: 'Daemon-app som anropar webb-API: er (flytta till produktion) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er (flytta till produktion)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c509e061c43c81f72682fb428529a8e72b34066a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056328"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app som anropar webb-API: er – flytta till produktion

Nu när du vet hur man hämtar och använder en token för ett tjänst-till-tjänst-anrop, lär du dig hur du flyttar din app till produktion.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Distribution – fall av daemon-appar för flera innehavare

Om du är en ISV som skapar ett daemon-program som kan köras i flera klienter måste du se till att klient administratörerna:

- Tillhandahåller ett tjänst huvud namn för programmet
- Ger medgivande till programmet

Du måste förklara vad kunderna har för att utföra dessa åtgärder. Mer information finns i [begära medgivande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Här följer några länkar för att lära dig mer:

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

- Om du inte redan har gjort det kan du prova snabb starten [Hämta en token och anropa Microsoft Graph API från en konsol app med appens identitet](./quickstart-v2-netcore-daemon.md).
- Referens dokumentation för:
  - Instansiera [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Anropar [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Andra exempel/Självstudier:
  - [Microsoft-Identity-Platform-Console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) innehåller ett enkelt .net Core daemon-konsolprogram som visar användare av en klient som frågar Microsoft Graph.

    ![topologi](media/scenario-daemon-app/daemon-app-sample.svg)

    Samma exempel illustrerar också variationen med certifikat.

    ![topologi](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) har ett ASP.NET MVC-webbprogram som synkroniserar data från Microsoft Graph att använda identiteten för programmet i stället för en användares räkning. Exemplet illustrerar även godkännande processen för administratörer.

    ![topologi](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL python är för närvarande en offentlig för hands version.
Mer information finns [i MSAL python: s in-lagringsplats-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample).

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j (MSAL. Java) är för närvarande en offentlig för hands version. Mer information finns i [MSAL Java in-lagringsplats-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
