---
title: Flytta en daemonapp som anropar webb-API:er till produktion – Microsoft identity-plattform | Azure
description: Lär dig hur du flyttar en daemonapp som anropar webb-API:er till produktion
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885437"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon app som anropar webb-API: er - flytta till produktion

Nu när du vet hur du hämtar och använder en token för ett service-to-service-samtal kan du läsa om hur du flyttar appen till produktion.

## <a name="deployment---multitenant-daemon-apps"></a>Distribution - multitenant daemon apps

Om du är en ISV skapa en demon program som kan köras i flera klienter, måste du se till att klienten admin:

- Avsättningar ett tjänstehuvudnamn för ansökan.
- Beviljar samtycke till ansökan.

Du måste förklara för dina kunder hur du utför dessa åtgärder. Mer information finns i [Begära samtycke för en hel klient.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Här är några länkar som hjälper dig att lära dig mer:

# <a name="net"></a>[.NET](#tab/dotnet)

- Snabbstart: [Skaffa en token och anropa Microsoft Graph API från en konsolapp med hjälp av appens identitet](./quickstart-v2-netcore-daemon.md).
- Referensdokumentation för:
  - Instansiera [KonfidentielltClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Anropa [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Andra exempel/självstudier:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) har ett enkelt .NET Core daemon konsolprogram som visar användarna av en klient som frågar Microsoft Graph.

    ![Exempel på apptopologi för daemon](media/scenario-daemon-app/daemon-app-sample.svg)

    Samma exempel illustrerar också en variant med certifikat:

    ![Exempel på apptopologi för daemon - certifikat](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) har en ASP.NET MVC-webbprogram som synkroniserar data från Microsoft Graph med hjälp av programmets identitet i stället för på uppdrag av en användare. Det här exemplet illustrerar också administratörsgodkännandeprocessen.

    ![topologi](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Prova [snabbstarten Hämta en token och anropa Microsoft Graph API från en Python-konsolapp med appens identitet](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java är för närvarande i offentlig förhandsversion. Mer information finns i [MSAL Java dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
