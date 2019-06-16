---
title: 'Daemon för appen anropa webb-API: er (flytta till produktion) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar en daemon-app att anrop webb-API: er (flytta till produktion)'
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545410"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app som anropar webb-API: er – flytta till produktion

Nu när du vet hur du hämtar och använder en token för en tjänst-till-tjänst-anrop, lär du dig hur du flyttar din app till produktion.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Distribution – fallet med flera innehavare daemon-appar

Om du är en ISV som skapar en daemonprogram som kan köras i flera klienter, måste du se till att innehavaradministratörer:

- Etablerar ett tjänsthuvudnamn för programmet
- Ger ditt medgivande till programmet

Du kommer att behöva förklara hur du utför dessa åtgärder för dina kunder. Mer information finns i [begär godkännande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Här följer några länkar till mer information:

### <a name="net"></a>.NET

- Om du inte redan har försök snabbstarten [hämta en token och anropa Microsoft Graph API från en konsolapp med hjälp av appens identitet](./quickstart-v2-netcore-daemon.md).
- Referensdokumentation för:
  - Instansiera [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Anropa [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Andra exempel/Självstudier:
  - [Microsoft identity-plattformen-konsolen-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) har ett enkelt .NET Core-daemon konsolprogram som visar användarna för en klient frågar Microsoft Graph.

    ![topologi](media/scenario-daemon-app/daemon-app-sample.svg)

    Samma prov visas också variationen med certifikat.

    ![topologi](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-WebApp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) funktioner ett ASP.NET MVC-webbprogram som synkroniserar data från Microsoft Graph med hjälp av identiteten för programmet i stället för för en användares räkning. Exemplet visar också admin medgivande processen.

    ![topologi](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python är för närvarande i offentlig förhandsversion. Mer information finns i [MSAL Python-klienten autentiseringsuppgifter i databasen exempel](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

MSAL Python är för närvarande i offentlig förhandsversion. Mer information finns i [MSAL Java i databasen-exempel](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).